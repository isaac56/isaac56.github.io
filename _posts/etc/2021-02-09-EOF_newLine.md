---
layout: post
title:  "EOF에 빈 줄이 있어야 하는 이유?"
excerpt: ""

categories:
  - etc
tags:
  - [Unix, Linux]

toc: true
toc_sticky: true
comments: true
 
date: 2021-02-09
last_modified_at: 2021-02-09
---
Git을 Terminal에서만 사용하고 있었다.

코드스쿼드의 코드리뷰 과정에서 EOF에 빈 줄을 넣는게 좋다는 소리를 알게 되었다.

Sourcetree로 빈줄이 없는 상태로 add해보니 "no new line at end of file"이라는 경고문구를 확인할 수 있었다.

왜 new line을 EOF에 넣어야 되지? 라는 걸 찾게 되었고 다른 블로그의 글을 복사하여 기록하여 놓았다.



## 왜 파일 마지막에 개행을 해야할까?

이유는 [POSIX](https://ko.wikipedia.org/wiki/POSIX) 명세가 그러하기 때문입니다. 명세에는 프로세스 환경, 파일과 디렉터리 등 다양한 개념을 규격화하고 있습니다.
여기에 텍스트 파일([Definitions - 3.392 Text File](https://pubs.opengroup.org/onlinepubs/000095399/basedefs/xbd_chap03.html#tag_03_392))과 라인([Definitions - 3.205 Line](https://pubs.opengroup.org/onlinepubs/000095399/basedefs/xbd_chap03.html#tag_03_205))에 대한 규격도 정의돼 있습니다.

한번 살펴보겠습니다.

- **Definitions - 3.392 Text File** : A file that contains characters organized into one or more lines. The lines do not contain NUL characters and none can exceed {LINE_MAX} bytes in length, including the Although IEEE Std 1003.1-2001 does not distinguish between text files and binary files (See the ISO C Standard), many utilities only produce predictable or meaningful output when operating on text files. The standard utilities that have such restrictions always specify “text files”in their STDIN or INPUT FILES sections.
- **Definitions - 3.205 Line** : A sequence of zero or more non- s plus a terminating .

이 두가지 명세를 종합해보면 재미있는 사실을 알 수 있습니다.

- 행의 끝(terminating)은 개행(EOL, end-of-line)
- 텍스트 파일은 행의 집합이며 행은 반드시 개행으로 끝난다.

따라서 많은 시스템과 도구들이 이 표준을 따라 구현되어 있습니다.
이를 지키지 않을 시 예기치 않은 동작을 일으킬 수 있다는 것이죠.

특히, 파일 마지막에 개행이 없다면 파일간의 차이를 알기 어렵습니다.

[![a.html과 b.html을 하나의 문장으로 출력](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.01.png)](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.01.png)<그림 1. a.html과 b.html을 하나의 문장으로 출력>

파일 마지막에 빈공간을 넣는다면 두 파일의 차이를 알 수 있습니다.

[![a.html과 b.html을 다른 문장으로 출력](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.02.png)](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.02.png)<그림 2. a.html과 b.html을 다른 문장으로 출력>

컴파일러인 gcc 역시 파일의 마지막에 개행이 없다면 경고합니다.

[![컴파일 에러](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.03.png)](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.03.png)<그림 3. 컴파일 에러>

깃허브도 마찬가지군요.

[![깃허브 경고](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.04.png)](https://blog.coderifleman.com/images/2015/text-files-end-with-a-newline/file_newline.04.png)<그림 4. 깃허브 경고>

이런 연유로 VIM과 같은 유닉스 에디터들은 자동적으로 파일의 마지막에 개행을 추가합니다.

만약 개발자 A가 개행을 하지 않고 커밋했는데, 개발자 B의 시스템이 자동으로 개행을 한다면 저장소에는 아무 정보도 없는 개행에 대한 로그가 남게 됩니다. 따라서 파일의 맨 마지막에 개행을 하나 추가하는 것은 필수라는 사실을 알 수 있습니다.

<br>

-출처 : [https://blog.coderifleman.com/2015/04/04/text-files-end-with-a-newline/](https://blog.coderifleman.com/2015/04/04/text-files-end-with-a-newline/)

