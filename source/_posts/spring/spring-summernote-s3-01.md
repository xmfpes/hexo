---
title: Spring Boot Summernote와 s3를 이용한 에디터 제작 - 01
date: 2017-09-23 15:10:03
tags:
- Spring Boot
- Jpa
- thymeleaf
- Summernote
- Amazon S3
categories:
- Spring
---

## **Spring Boot Summernote 에디터에 Amazon s3 연동하기 - 01**

![spring](/images/spring.png)

### Summernote 삽입 이미지 s3 업로드 및 tag 형식으로 삽입하기

## STEP 1. 아마존 access Key Id 및 Secret access key 발급

s3에 이미지나 파일을 업로드 하기 위해, 엑세스 키 및 시크릿 엑세스 키를 발급한다.

https://console.aws.amazon.com/iam/home#/security_credential

위의 페이지에 접속해서 Security_credential에 접속한다.

Access keys (access key ID and secret access key) 탭 클릭

Create New Access Key 키 버튼 눌러서, 엑세스 키 및 엑세스 비밀번호 생성


## STEP 2. Eclipse에 AWS Toolkit 설치 및 액세스 키 적용

우선 Eclipse 마켓플레이스에 접속해서
aws를 검색한 후 아래와 같은것을 찾아서 설치합니다.

![aws](/images/aws/marketaws.png)

저는 설치 중 아래와 같은 에러가 발생했는데, 혹시 비슷한 에러가 나면 참고하시면 될것 같습니다.
```bash
Software currently installed: Amazon RDS Management 1.0.0.v201501061106 (com.amazonaws.eclipse.rds.feature.feature.group 1.0.0.v201501061106)
Missing requirement: RDS 1.0.0.v201501061106 (com.amazonaws.eclipse.rds 1.0.0.v201501061106) requires 'bundle org.eclipse.datatools.connectivity.ui.dse 1.1.0' but it could not be found
```

![aws](/images/aws/dtp.png)

저는 위의 에러가 발생하고, StackOverFlow를 찾아보았는데 위의 DTP를 설치한 이후 해결되었습니다.

설치하고 이클립스를 재시작하면 아래와 같은 액세스 키 ID와 시크릿 키를 입력하는 창이 나옵니다.
![tookit](/images/aws/toolkit.png)

1번 항목에 본인의 아이디와 시크릿 키를 입력한 이후 Finish를 누르면 됩니다.

이후에는 따로 키 설정을 해 줄 필요가 없습니다.

추후 변경이 필요하면, 아래의 이클립스 환경 설정에 들어가서 변경하시면 됩니다.

![setting](/images/aws/setting.png)

## STEP 3. 프로젝트 기본 설정

MySQL / Lombok / thymeleaf / JPA(ORM) / Maven

위의 패키지들을 추가한 Spring starter 프로젝트를 생성하고

application.properties에 데이터베이스 설정 내용을 추가합니다.
```
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.url=jdbc:mysql://localhost:3306/YOURDBNAME?autoReconnect=true&useSSL=false&useUnicode=true&characterEncoding=utf8
spring.datasource.username=USERNAME
spring.datasource.password=PASSWORD
spring.thymeleaf.cache=false
```

## STEP 4. CRUD 제작을 위한 기본 셋팅

우선 에디터를 적용하기 위해서는 기본적인 CRUD가 적용이 되어 있어야 하므로

기본적인 CRUD 게시판을 제작합니다.

우선, 사용할 패키지들을 미리 만들어 둡니다.

- web : 컨트롤러를 저장할 패키지
- domain : VO 객체와 JPA Repository를 저장할 패키지
- aws : Amazon S3 업로드 설정 관련 클래스를 저장할 패키지

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

/templates/includes/header.html
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

/templates/includes/footer.html

footer의 내용은 나중에 작성하고, 일단 생성만 해 둔다.

게시글 리스트, 작성 화면, 게시글 상세 보기 뷰 작성

/templates/article/list.html
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
                		<a th:href="@{/article/articleDetail/{articleId}(articleId=${article.id})}" th:text="${article.title}"></a>
                </td>
                <td th:text="${article.regDate}">
                </td>
                <td>
                		<a th:href="@{/article/delete/{articleId}(articleId=${article.id})}" class="btn btn-danger">삭제</a>
                </td>
            </tr>
        </table>
        <a href="/article/write" class="btn btn-primary">작성하기</a>
    </div>
    <div th:replace="/includes/footer"></div>
</body>
</html>
```
/templates/article/form.html
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
/templates/article/articleDetail.html
```html
<!DOCTYPE html>
<html lang="en">
<div th:replace="/includes/header"></div>
<body>
	<div class="container" style="padding-top: 100px;">
		<div class="panel panel-default">
			<div class="panel-heading" th:text="${article.title}"></div>
			<div class="panel-body">
				<div th:text="|작성일 : ${article.regDate}|" style="padding-bottom: 10px"></div>
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



## STEP 5. CRUD 기능 완성하기

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
		return "redirect:/article/list";
	}
}

```

위의 컨트롤러를 정의하면
기본적인 리스트 출력, 글 상세 보기, 글 작성하기, 글 삭제하기 기능이 정상 작동할 것입니다.

## STEP 6. Summernote 적용하기

글 작성에서 에디터를 적용하기 위해서
/templates/article/form 에 javascript 코드를 추가합니다.

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

아래의 코드를 추가하면, textarea에 summernote 에디터가 적용되어 나타나게 됩니다.

지금도 클립보드 이미지를 붙여넣는것은 가능하지만, 글에 태그가 적용되는것이 아닌 파일을 바이너리 형태로 저장하기 때문에 관리가 불가능하기에 기능을 수정해야합니다.

클립보드의 이미지를 붙여넣었을 경우, s3에 업로드 하고 해당 이미지의 path를 가지는 img 태그를 글 내용에 넣어주어야 관리가 가능합니다.

위의 summernote 코드를 아래의 코드로 수정해서 이미지가 s3에 저장되도록 변경합니다.
summernote 에디터에 사진이 추가되면 onImageUpload를 통해 이미지를 ajax를 통해 s3에 업로드 하고 업로드 된 파일 경로를 받아와서 summernote에 이미지 태그 형태로 추가해주는 코드입니다.


```javascript
<script>
$(document).ready(function() {
	var sendFile = function (file, el) {
	      var form_data = new FormData();
	      form_data.append('file', file);
	      $.ajax({
	        data: form_data,
	        type: "POST",
	        url: '/file',
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


위의 코드는 지금 ajax 파일 업로드 기능이 제대로 구현되어 있지 않으므로 작동하지 않는데,
아래에서 S3Manager 클래스와 FileController 클래스를 추가한 후에 확인할겁니다.

## STEP 7. 파일을 받아 처리해주는 FileController 와 S3 업로드를 담당하는 클래스 생성하기

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
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
import org.springframework.web.multipart.MultipartFile;
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
	private static final String amazonPath = "https://s3.ap-northeast-2.amazonaws.com/kyubucket/";
	private static AmazonS3 s3Cliient = new AmazonS3Client(new ProfileCredentialsProvider());
	private static ObjectMetadata meta;
	public String saveUploadedFiles(MultipartFile file) throws IOException {
		if (file.isEmpty()) {
			System.out.println("file not found");
		}
		byte[] bytes = file.getBytes();
		String randomFileName = UUID.randomUUID() + file.getOriginalFilename();
		sendFiles(randomFileName, bytes);
		return amazonPath + randomFileName;
	}
	public static boolean sendFiles(String name, byte[] file) {
		InputStream is = new ByteArrayInputStream(file);
		try {
			s3Cliient.putObject(makeRequest(is, name));
			return true;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return false;
	}
	public static PutObjectRequest makeRequest(InputStream is, String fileName) {
		ObjectMetadata uploadMetaData = new ObjectMetadata();
		return new PutObjectRequest("kyubucket", fileName, is, uploadMetaData).withCannedAcl(CannedAccessControlList.PublicRead);
	}
}
```

FileController 추가하기
```java
import java.io.IOException;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;
import com.kyunam.aws.S3Manager;

@Controller
@RequestMapping("/file")
public class FileController {

	@PostMapping("")
	public ResponseEntity<?> handleImageUpload(@RequestParam("file") MultipartFile file) {
		String fileName;
		try {
			if (file.isEmpty()) {
				return new ResponseEntity("please select a file!", HttpStatus.OK);
			}
			S3Manager s3Manager = new S3Manager();
			fileName = s3Manager.saveUploadedFiles(file);
		} catch (IOException e) {
			e.printStackTrace();
			return ResponseEntity.badRequest().build();
		}
		return ResponseEntity.ok().body(fileName);
	}
}
```

이제 클립보드에 있는 이미지를 붙여넣었을 경우, s3 버킷에 저장이 되어야 하고 글에도 정상적으로
출력이 되어야 합니다.

ClassNotFoundException: com.amazonaws.services.s3.AmazonS3Client
이런 에러가 뜨면.. maven이 제대로 안 받아진거니까 껐다 키던가 clean 하던가 업데이트 할것

다음으로 다룰 내용은, 에디터에서 이미지 업로드를 하는 것 이외에도 따로 파일 업로드를 하는 기능을 추가할 것이다.

오늘 학습에 사용된 코드는 깃허브 [썸머노트 레파지토리](https://github.com/xmfpes/spring-editor-fileupload/commits/master)에 커밋 로그로 남겨두었습니다.
