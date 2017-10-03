---
title: Spring Boot Summernote와 s3를 이용한 에디터 제작 - 02
date: 2017-09-25 15:10:03
tags:
- Spring Boot
- Jpa
- thymeleaf
- Summernote
- Amazon S3
categories:
- Spring
---

## **Spring Boot Summernote 에디터에 Amazon s3 연동하기 - 02**

![spring](/images/spring.png)

### Form에서 파일 업로드를 통해 s3에 업로드 하고 상세 페이지에서 파일 리스트 확인하기

## STEP 1. 파일 업로드를 위해 뷰 파일 수정하기

/article/form.html의 내용을 아래와 같이 수정한다

수정되는 부분은 input type=file 폼을 추가하는 부분과

javascript를 이용해 파일 폼을 추가해주는 코드 두가지이다.

```html
<!DOCTYPE html>
<html lang="en">
<div th:replace="/includes/header"></div>
<body>
    <div class="container" style="padding-top: 100px;">
        <form action="" class="boardSubmit" method="post" enctype="multipart/form-data">
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
                <tr>
                		<th>파일</th>
                		<td>
                			<div class="fileForm">
                				<input type="file" name="uploadFiles"/>
                			</div>
                		</td>
                </tr>
            </table>

            <button class="btn btn-primary">작성하기</button>
            <button type="button" class="fileAdd btn btn-success">파일 추가</button>
        </form>
    </div>
    <div th:replace="/includes/footer"></div>
</body>
</html>
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
<script>
$(document).ready(function(){
	$('.fileAdd').click(function(){
		if($('.fileForm').children().length >= 5){
			alert("파일은 5개 까지만 추가 가능합니다.");
		}else{
			$('.fileForm').append('<input type="file" name="uploadFiles"/>');
		}
	})
});
</script>
```

## STEP 2. domain 패키지의 모델 구조 수정하기

JPA에 관계를 조금 수정해 주어야 합니다. 여기서 아래의 어노테이션이 등장합니다.

- @OneToMany

엔티티간 연관관계를 설정해주기 위한 어노테이션

- @JoinColumn

Article.java에 저장할 파일의 리스트가 저장될 객체를 추가합니다.

글 하나에는, 여러 파일이 추가 될 수 있으므로 외부 키로 파일 리스트를 가지게 됩니다. 이는 OneToMany의 관계로 추가합니다.

게시글에서만 파일의 리스트를 출력해주면 되기 때문에 Article 쪽에서 단방향(Unidirectional)으로 연관관계를 설정할 것입니다.

Article에서만 List<ArticleFiles> files를 추가하고

ArticleFiles 객체에서는 Article의 관계를 추가해주지 않는 구조로 단방향으로 설계됩니다.

여기서 OneToMany 관계를 통해 연관관계를 설정하면, 테이블이 2개만 생성 될 것 같지만 확인해 보면 3개가 생성되어 있습니다.

이는 Article 컬럼이 생성될때 ArticleFiles에 하나가 아닌 여러개 파일의 id 값을 저장해야 하기 때문에 OneToMany 관계가 설정되면 JPA에서는 여러 개의 데이터를 저장하기 위해 별도의 테이블을 생성하기 때문입니다.

이 연관관계에서 별도의 테이블이 생성되는것이 싫다면 @JoinTable 어노테이션이나 @JoinColumn을 통해 기존 테이블을 이용해 조인한다고 표현해 주어야 합니다.

@JoinColumn을 이용해 이미 존재하는 테이블에 컬럼을 추가해 이 컬럼을 이용해 조인해서 데이터를 가져올 때 사용합니다.

- @OneToMany(cascade=CascadeType.ALL)


#### cascade=CascadeType.ALL

아래 엔티티에서 cascade=CascadeType.ALL 설정을 주지 않는다면 JPA에서 한번에 여러 엔티티 객체의 상태를 변경해 주는 과정에서 에러가 발생합니다.

이는 종속적인 엔티티 객체들의 영속성을 같이 처리하는 '영속성 전이'라는 개념에 대한 설정이 빠져있기 때문인데

cascade=CascadeType.ALL을 통해 모든 변경에 대해 영속성 전이를 처리하도록 설정해 준 것입니다.

### Article.java

```java
import java.util.Date;
import java.util.List;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.OneToMany;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

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

    @OneToMany(cascade=CascadeType.ALL)
    @JoinColumn(name="article_id")
    List<ArticleFiles> files;

```

### ArticleFiles.java
파일의 원래 이름을 저장할 fileName, 파일이 저장된 경로를 저장할 fielPath를 가집니다.
```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Entity
@Getter
@Setter
@ToString
public class ArticleFiles {
	@Id
	@GeneratedValue
	private Long id;

	String fileName;
	String filePath;

}
```

위와같이 엔티티들을 생성하고 나면 아래와 같이 데이터베이스가 생성됩니다.

![db](/images/spring/db.png)

## STEP 3. 파일 업로드를 위해 컨트롤러 내용 수정하기

파일을 받아서 처리해주도록 컨트롤러의 내용을 수정해야 합니다.

기존에는 Article article을 통해 폼의 데이터를 받아왔는데, file 데이터가 추가되어서
아래처럼 title, content와 uploadFiles를 따로 받아오도록 수정했습니다.

스프링의 기능을 이용해, Article로 받아올 수 있을것 같지만 아직 방법을 찾지 못했기 때문에 아래와 같이 처리합니다.

아래의 컨트롤러는 파일의 리스트를 받아 S3Manager를 통해 파일을 s3에 업로드 하고  ArticleFiles 객체를 생성해 list에 파일 이름과 경로를 설정해 넣어주고 Article 객체에 데이터와 리스트를 넣은 후 Repository를 이용해 저장하는 방식으로 구현했습니다.

```java
@PostMapping("/write")
public String write(String title, String content, List<MultipartFile> uploadFiles) throws IOException {  

		S3Manager s3manager = new S3Manager();
		List<ArticleFiles> list = new ArrayList<ArticleFiles>();
		for(MultipartFile file : uploadFiles) {
			ArticleFiles articleFile = new ArticleFiles();
			articleFile.setFileName(file.getOriginalFilename());
			articleFile.setFilePath(s3manager.saveUploadedFiles(file));
			list.add(articleFile);
		}

		Article article = new Article();
		article.setTitle(title);
		article.setContent(content);
		article.setFiles(list);
    article.setRegDate(new Date());

    articleRepository.save(article);
    return "redirect:/article/list";
}
```

정상적으로 처리가 된 것을 확인하고, 디테일 페이지에서 파일 리스트를 추가해줍니다.

## STEP 4. 업로드된 파일을 확인하는 디테일 페이지 뷰 수정하기

/article/articleDetail.html
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
                파일 리스트
                <div th:each="file : ${article.files}">
                		 <a th:href="${file.filePath}" th:text="${file.fileName}" th:attr="download=${file.fileName}">
                		 </a>
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

위와 같이 작성하면 업로드된 파일 리스트가 뜨고, 정상적으로 다운로드 됩니다.

- 수정 사항
  - 다운로드 파일 이름 변경
  - deprecated된 Amazon s3 코드 변경하기

완성된 에디터

![editor](/images/spring/editor.png)

오늘 학습에 사용된 코드는 깃허브 [썸머노트 레파지토리](https://github.com/xmfpes/spring-editor-fileupload/commit/90a07fbb79b402980719f16ef99c980f7ce90809)에 커밋 로그로 남겨두었습니다.
