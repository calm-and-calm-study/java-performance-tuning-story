## httpd.conf 
- Apache의 기본이자 핵심적인 설정 파일로 Apache의 대부분 핵심 기능을 여기에서 설정함.

## 기본적인 설정 요소 
 - ServerRoot : Apache 서버가 존재하는 디렉토리를 설정. 절대경로로 설정해줘야 함.     
               일반적으로 하위 디렉토리 conf및 logs 디렉토리 위치를 찾기 위해 사용함.

 - Listen : Apache가 가동되는 동안 수신할 포트 번호를 지정함. 여러 포트 번호를 수신하려면 Listen을 여러번 선언하면 된다. 
            더 나아가, 특정 포트는 특정 IP로만 접속되게 하고 싶을 경우 Listen 특정IP:특정포트 이렇게 지정하면 된다. 
            TLS(SSL)를 지원하기 위한 443 포트는 httpd-ssl.conf 파일에서 따로 설정함

- LoadModule : ${ServerRoot}/modules 디렉토리 안에 있는 모듈을(so 파일)을 읽어들이고, 사용 가능한 모듈 목록에 추가함.
               Apache와 호환되는 모듈을 로드  LoadModule {modname}  *.* 대부분 *.so확장자를 가지는데 모든 모듈이 그렇지만은 않다. 

- Include : Apache는 설정 파일을 분산시켜 저장하여 필요한 설정 파일만 로드할 수 있는데 이때 사용하는 명령어
            Include conf/extra/httpd-ssl.conf 라고 지정할 경우 SSL을 지원하기 위해 필요한 설정 파일을 포함시킴

## ‘Main’ Server Configration
   이 라인 이후부터  작성된 설정들은 메인 서버 설정
   가상 호스트를 생성할 때 이 메인 서버 설정들이 각 가상호스트의 기본값으로 상속된다.

- ServerAdmin : 서버에서 오류가 발생했을때 클라이언트로 전송하는 오류 메세지에 들어갈 이메일 주소를 설정

- ServerName : 서버를 식별하기 위한 호스트 이름을 지정하고, 등록된 도메인이 없다면 IP 주소를 대신 적을 수도 있다.

- DocumentRoot : 웹 페이지의 루트를 지정하는 지시자 
                 DocumentRoot 마지막에 슬래시(/) 지정하면 안됨.
                 <Directory> 지시자를 적용하면 별도의 <Directory> 지시자를 사용하지 않는 한 그 이하의 서브 디렉터리들도 모두 이 지시자의 속성을 상속받는다

  ### httpd.conf의 메인 서버 설정에 있는 최상위 <Directory>
      `<Directory />`
      `AllowOverride none`
      `Require all denied`
      `</Directory>`

  웹 서버의 파일 시스템 루트(/)에 대한 설정
  Apache는 일단 모든 디렉터리의 액세스를 금지시키고 서비스를 제공할 디렉터리만 새로 설정하여 허용시키는, 선금지 후허용 원칙을 기본 설정값으로 하고 있다

  ### htdocs 폴더에 액세스 권한을 부여
      `<Directory "${SRVROOT}/htdocs">`
      `Options FllowSymLink`
      `AllowOverride All`
      `Require all granted`
      `</Directory>`

- Options :  이 지시자는 이 디렉터리가 어떤 용도로 사용되는지를 정의
            - FllowSymLinks: 이 디렉터리 내에서 심볼릭 링크를 가능하게 한다.
            - Indexes: 디렉터리 경로가 요청되고 DirectoryIndex에 맞는 파일이 없으면 해당 디렉터리의 목록을 출력한다.
            - Includes: SSI(Server Side Include)를 허용한다.
            - IncludesNOEXEC: SSI는 가능하나 SSI 내에서의 CGI는 불가
            - ExecCGI: CGI 가능, 자세한 내용은 후술
            - All: MultiViews 옵션을 제외한 모든 옵션을 적용한다.
            - None: 어떠한 옵션도 적용하지 않는다.

- AllowOverride : 디렉터리의 설정 내용을 별도의 외부 파일(.htaccess)에서 재설정 또는 덮어쓸 수 있는지 여부를 결정.httpd.conf 설정이 무시되고 외부 파일의 내용이 적용됨 (All | None)

- Require : 해당 디렉토리의 접근 허용 여부를 설정한다.

- Alias : 웹 서버 상의 경로와 URL 상의 경로를 연결하는 작업을 매핑(Mapping)이라 하며, 매핑시 사용되는 지시자 

- Redirect : URL을 다른 URL로 리다이렉트 시킴

- ErrorDocument : 오류 페이지를 출력할 때 사용자 정의 페이지를 보여주기 위해선 이 지시자를 사용한다.
  
  
  
