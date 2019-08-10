---
layout: post
title: "간단한 Debian-Package 제작해보기"
tags: ["Debian", "Linux"]
comments: true
---

간단한 Debian-Package 제작해보기

---

## 시작하면서
업무를 하면서 Ubuntu 환경에서 돌아가는 Package를 만들어야 할 일이 생겼습니다.
습관적으로 Apt-get 을 사용해서 패키지를 당겨오고 설치만 해봤지 만들어 본적은 한번도 없었습니다.
여러 우여곡절들이 있었지만 꽤나 재미있고 신선했던 일이라서 포스팅으로 남겨볼까합니다.

## Control 
Control 파일은 데비안 형식의 패키지의 모든 정보를 담고 있는 파일입니다.

``` 
Package: Sample Debian Package
Version: 0.0.1
Section: devel
Priority: optional
Architecture: all
Depends: python3
Maintainer : lntuition
Description : Sample debian packaging example
```

- Package (필수): 데비안 패키지 이름입니다.
- Version (필수): 버전명입니다.
- Section : 데비안 패키지에서는 섹션별로 control record(?)를 다르게 저장한다고 하네요. 사용할 수 있는 [리스트 옵션](https://packages.debian.org/unstable/)은 다음과 같고, 여기서는 개발(devel) 옵션을 사용하겠습니다.
- Priority : 패키지의 우선순의를 의미합니다. required → important → standard → optional → extra 순으로 정의되어있습니다.
- Architecture (필수): 패키지가 Architecture을 의미합니다. 가장 크게 주로 쓰이는 옵션은 all과 any가 있습니다. all의 경우는 아키텍쳐와 상관없이 사용할 수 있는 패키지만 빌드하는 옵션이고, any의 경우에는 빌드 패키지와 무관하게 모든 아키텍쳐에서 잘 컴파일 되어야함을 의미합니다.
- Depends : 의존성이 있는 패키지를 나타냅니다.
- Maintainer (필수) : maintainer 이름입니다.
- Description (필수) : 패키지 설명입니다.

## 구조
데비안 패키지 폴더의 구조는 Root와 대응됩니다. 
즉 내가 /usr/local/lib에 설치하고 싶다면 폴더의 구조를 같게 해주면 되는것이죠.
즉 Debian Package의 구조는 보통 아래처럼 구성됩니다.
<center>
    <img src="/images/Make-debian-package-tree.png">
</center>

## postinst / postrm
usr/local/lib에 설치되어 있는 라이브러리를 활성하기 위해서는 `sudo ldconfig /usr/local/lib` 명령어를 실행시켜 주어야 합니다.
하지만 저희가 매번 apt 등을 이용하여 설치시에 이 명령어를 실행하는 것은 아니죠.
우리는 이것을 postinst 파일에 기록함으로서 자동 실행하게 만들 수 있습니다.
파일명에서 볼 수 있듯이 postinst은 설치가 끝 난 후에 자동으로 실행되는 스크립트 임을 알 수 있죠.
유사하게 패키지가 삭제된 후에 어떤 파일을 삭제 하고 싶다거나 라는 일이 있으면 같은 방식으로 스크립트를 postrm에 기술해 주면됩니다.
이때 두 스크립트를 실행하기 위해서 chmod 를 통해 권한(755)를 부여해 주어야합니다.

## 빌드/설치
빌드의 경우에는 `dpkg -b <Directory>` 옵션으로 데비안 패키지를 만들 수 있습니다.
다만 설치의 경우에 `dpkg -i <Package>` 를 이용하면 package에 걸려 있는 dependency를 check만 할 뿐 설치해 주지는 않죠
이를 해결하기 위해서는 `apt -i <Package>` 를 이용하여 설치해 주면 해결이 됩니다.
apt는 dpkg 보다 높은 level의 tool 이므로 자동으로 dependency를 찾아주고 설치까지 해줍니다.

## 끝내면서
패키징을 처음 해보면서 드는 생각은 '생각보다는 할만하네?' 였습니다.
새로운 것을 알아가는 과정은 언제나 즐겁네요.
<center> <b> "앞으로도 지나가는 모든일에 재미를 붙일 수 있도록 해봐야겠습니다." </b> </center>

---
참고 : [https://www.debian.org/doc/debian-policy/ch-controlfields.html](https://www.debian.org/doc/debian-policy/ch-controlfields.html)