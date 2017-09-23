---
title: Spring Boot와 Summernote 에디터 연동하기
date: 2017-09-23 15:10:03
tags:
- Spring Boot
- Jpa
- thymeleaf
- Summernote
- Amazon S3
categories:
- Spring
thumbnail: /images/spring.png
---

## **Spring Boot Summernote 에디터 연동 및 S3를 이용한 이미지 업로드**

MySQL / Lombok / thymeleaf / JPA(ORM) / Maven

위의 패키지들을 추가한 프로젝트를 생성하고

application.properties에 아래 내용 추가
```
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.url=jdbc:mysql://localhost:3306/kyunam?autoReconnect=true&useSSL=false&useUnicode=true&characterEncoding=utf8
spring.datasource.username=root
spring.datasource.password=1111
spring.thymeleaf.cache=false
```

## STEP 1. 게시판 제작을 위한 기본 셋팅

우선, 사용할 패키지들을 미리 만들어 둔다

web : 컨트롤러를 저장할 패키지
domain : VO 객체와 JPA Repository를 저장할 패키지
amazon : Amazon S3 업로드 설정 관련 클래스를 저장할 패키지

- ### domain 패키지에 게시글의 정보를 저장할 Article 객체 생성

```java
@Entity
@Getter
@Setter
@ToString
public class Article {
	@Id
    @GeneratedValue
    Long id;

    String title;

    @Column(length = 100000000)
    String content;

    Date regDate;

}
```

Lombok 설정은 알아서 할것.

- ### domain 패키지 내부에 ArticleRepository 인터페이스를 생성한다.

```java
public interface ArticleRepository extends JpaRepository<Article, Long> {

}
```

- ### Templates 폴더 내부에 게시글 작성을 위한 뷰 파일 작성
공통 파일인 header 생성

아래의 스크립트와 링크에는 Summernote 관련 설정도 추가되어 있다.

/templates/includes/header
```html
<head>
    <meta charset="UTF-8"/>
    <meta http-equiv="content-type" content="text/html; charset=UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <meta http-equiv="X-UA-Compatible" content="ie=edge"/>
    <title>Spring Boot</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"/>
	<script src="https://code.jquery.com/jquery-3.2.1.min.js" integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4=" crossorigin="anonymous"></script>
	<script src="http://netdna.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.js"></script>

	<link href="http://cdnjs.cloudflare.com/ajax/libs/summernote/0.8.2/summernote.css" rel="stylesheet"/>
	<script src="http://cdnjs.cloudflare.com/ajax/libs/summernote/0.8.2/summernote.js"></script>
</head>

```

게시글 리스트, 작성 화면, 게시글 상세 보기 뷰 작성

/templates/article/list
```html
<!DOCTYPE html>
<html lang="en">
<div th:replace="/includes/header"></div>
<body>
	<div class="container" style="padding-top: 100px;">
		<table class="table table-bordered table-hover">
			<tr>
				<th style="width:8%;">글 번호</th>
				<th>제목</th>
				<th>작성일</th>
				<th>삭제</th>
			</tr>
			<tr th:each="article : ${articles}">
		        <td th:text="${article.id}"></td>
		        <td>
		        		<a th:href="@{/article/articleDetail/{articleId}(articleId=${article.id})}" th:text="${article.title}"></a></td>
		        <td th:text="${article.regDate}"></td>
		        <td><a th:href="@{/article/delete/{articleId}(articleId=${article.id})}" class="btn btn-danger">삭제</a></td>
		    </tr>
		</table>
		<a href="/article/write" class="btn btn-primary">작성하기</a>
	</div>
	<div th:replace="/includes/footer"></div>
</body>
</html>
```
/templates/article/form
```html
<!DOCTYPE html>
<html lang="en">
<div th:replace="/includes/header"></div>
<body>
	<div class="container" style="padding-top: 100px;">
		<form action="" class="boardSubmit" method="post">
			<table class="table table-bordered">
				<tr>
					<th>글 제목</th>
					<td><input type="text" name="title" class="form-control" /></td>
				</tr>
				<tr>
					<th>글 내용</th>
					<td><textarea class="form-control" id="summernote"
							name="content" placeholder="content" maxlength="140" rows="7"></textarea>
					</td>
				</tr>
			</table>
			<button class="btn btn-primary">작성하기</button>
		</form>

	</div>
	<div th:replace="/includes/footer"></div>
</body>
</html>
```
/templates/article/articleDetail
```html
<!DOCTYPE html>
<html lang="en">
<div th:replace="/includes/header"></div>
<body>
	<div class="container" style="padding-top: 100px;">
		<div class="panel panel-default">
			<div class="panel-heading" th:text="${article.title}"></div>
			<div class="panel-body">
				<div th:utext="@{작성일 : {articleDate}(articleDate=${article.regDate})}" style="padding-bottom: 10px">
				</div>
				<div th:utext="${article.content}"></div>
			</div>
		</div>
		<a href="/article/list" class="btn btn-default">목록으로</a>
		<a href="/admin/products/edit/" class="btn btn-primary">수정</a>
	</div>
	<div th:replace="/includes/footer"></div>
</body>
</html>

```





## STEP 2. CRUD 기능 완성하기

Article을 담당할 ArticleController 생성
```java

import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import com.kyunam.domain.Article;
import com.kyunam.domain.ArticleRepository;

@Controller
@RequestMapping("/article")
public class ArticleController {

	@Autowired
	ArticleRepository articleRepository;

	@GetMapping("/list")
	public String list(Model model) {
		model.addAttribute("articles", articleRepository.findAll());
		return "/article/list";
	}

	@GetMapping("/write")
	public String writeForm() {
		return "/article/form";
	}

	@PostMapping("/write")
	public String write(Article article) {
		article.setRegDate(new Date());
		articleRepository.save(article);
		return "redirect:/article/list";
	}

	@GetMapping("/articleDetail/{id}")
	public String detail(@PathVariable Long id, Model model) {
		model.addAttribute("article",articleRepository.findOne(id));

		return "/article/articleDetail";
	}

	@GetMapping("/delete/{id}")
	public String deleteArticle(@PathVariable Long id) {
		articleRepository.delete(id);
		return "/article/list";
	}
}

```

위의 컨트롤러를 정의하면
기본적인 리스트 출력, 글 상세 보기, 글 작성하기, 글 삭제하기 기능이 정상 작동한다.

## STEP 3. Summernote 적용하기

글 작성에서 에디터가 적용되어야 한다.
/templates/article/form 에 javascript 코드를 추가한다.

```javascript
<script>
$(document).ready(function() {

	$('#summernote').summernote({
        height: 300,
        minHeight: null,
        maxHeight: null,
        focus: true
      });

});
</script>
```

아래의 코드를 추가하면, textarea에 summernote 에디터가 적용되어 출력된다.

지금도 클립보드 이미지를 붙여넣는것은 가능하지만, 글에 태그가 적용되는것이 아닌 파일 형태로
저장되어 관리가 불가능하기에 기능을 수정한다.

아래의 코드는 summernote 에디터에 사진이 추가되면

onImageUpload를 통해 이미지를 ajax를 통해 s3에 업로드 하고
업로드 된 파일 경로를 받아와서 summernote에 이미지 태그 형태로 추가해주는 코드이다.


```javascript
<script>
$(document).ready(function() {
	var sendFile = function (file, el) {
	      var form_data = new FormData();
	      form_data.append('file', file);
	      $.ajax({
	        data: form_data,
	        type: "POST",
	        url: '/file/image',
	        cache: false,
	        contentType: false,
	        enctype: 'multipart/form-data',
	        processData: false,
	        success: function(url) {
	        		$('#summernote').summernote('insertImage', url);
		        $('#imageBoard > ul').append('<li><img src="'+ url +'" width="480" height="auto"/></li>');

	        }
	      });
	    }
	$('#summernote').summernote({
        height: 300,
        minHeight: null,
        maxHeight: null,
        focus: true,
        callbacks: {
          onImageUpload: function(files, editor, welEditable) {
            for (var i = files.length - 1; i >= 0; i--) {
              sendFile(files[i], this);
            }
          }
        }
      });

});
</script>
```


위의 코드는 지금 ajax 파일 업로드 기능이 제대로 구현되어 있지 않으므로 작동하지 않는다.
아래에서 S3Manager 클래스와 FileController 클래스를 추가한 후 확인하자.

## STEP 4. 파일을 받아 처리해주는 FileController 와 S3 업로드를 담당하는 클래스 생성하기

pom.xml에 Amazon S3 Maven 추가하기
```xml
<dependency>
		<groupId>com.amazonaws</groupId>
		<artifactId>aws-java-sdk-s3</artifactId>
		<version>1.11.200</version>
</dependency>
```
com.kyunam.amazon 패키지 아래에

S3Manager 클래스 추가하기

```java
package com.kyunam.amazon;

import java.io.ByteArrayInputStream;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.model.CannedAccessControlList;
import com.amazonaws.services.s3.model.DeleteObjectsRequest;
import com.amazonaws.services.s3.model.DeleteObjectsResult;
import com.amazonaws.services.s3.model.MultiObjectDeleteException;
import com.amazonaws.services.s3.model.ObjectMetadata;
import com.amazonaws.services.s3.model.PutObjectRequest;
import com.amazonaws.services.s3.model.DeleteObjectsRequest.KeyVersion;

public class S3Manager {

	private static AmazonS3 s3Cliient = new AmazonS3Client(new ProfileCredentialsProvider());
	private static ObjectMetadata meta;

	public static boolean sendFiles(String name, byte[] file) {
		InputStream is = new ByteArrayInputStream(file);
		try {
			s3Cliient.putObject(makeRequest(is, name));
			return true;

		} catch (Exception e) {
			System.out.println(e);
		}
		return false;
	}

	public static PutObjectRequest makeRequest(InputStream is, String fileName) {
		ObjectMetadata uploadMetaData = new ObjectMetadata();
		uploadMetaData.setContentType("image/png/jpg");
		return new PutObjectRequest("kyubucket", fileName, is, uploadMetaData).withCannedAcl(CannedAccessControlList.PublicRead);
	}
}
```

FileController 추가하기
```java
package com.kyunam.web;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.UUID;

import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import com.amazonaws.services.s3.model.DeleteObjectsRequest.KeyVersion;
import com.kyunam.amazon.S3Manager;

@Controller
@RequestMapping("/file")
public class FileController {

	private static final String amazonPath = "https://s3.ap-northeast-2.amazonaws.com/kyubucket/";

	@PostMapping("/image")
	public ResponseEntity<?> handleImageUpload(@RequestParam("file") MultipartFile file) {
		String fileName;
		try {
			if (file.isEmpty()) {
				return new ResponseEntity("please select a file!", HttpStatus.OK);
			}
			fileName = saveUploadedFiles(file);
		} catch (IOException e) {
			return ResponseEntity.badRequest().build();
		}
		return ResponseEntity.ok().body(fileName);
	}

	private String saveUploadedFiles(MultipartFile file) throws IOException {

		if (file.isEmpty()) {
			System.out.println("file not found");
		}
		S3Manager s3Manager = new S3Manager();
		byte[] bytes = file.getBytes();
		String randomFileName = UUID.randomUUID() + file.getOriginalFilename();
		s3Manager.sendFiles(randomFileName, bytes);
		return amazonPath + randomFileName;
	}
}
```

아마도 제대로 작동할 것이다. 안되면 뭔가 잘못됐다.

ClassNotFoundException: com.amazonaws.services.s3.AmazonS3Client
이런 에러가 뜨면.. maven이 제대로 안 받아진거니까 껐다 키던가 clean 하던가 업데이트 할것
