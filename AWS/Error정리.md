## AWS EC2를 이용해서 React(Client) + Node.js(Server) 웹사이트 배포하기

react npm install이 안된다 <br>
==> client는 npm install시 에러뜸 Cannot read properties of null (reading 'children')<br>
==> package-lock.json 삭제하고 npm install --force해줌<br>

npm start시 body-parser module not found 에러가 뜨는데 관련 모듈을 삭제 후 다시 설치해도 똑같은 에러 발생<br>
그러나 에러 발생 후 시간 좀 지나면 server starting이 뜨지만 서버 코드 동작 스킵하고 프론트만 동작<br>
==> node와 npm의 버전을 로컬과 같은 버전으로 깔아 주었으나 실패<br>
==>Express 4.16버전 이상부터는 내장모듈로 포함되어 있어서 별도 설치가 필요 없다고 해서 그냥 서버 쪽 코드에 있는 body-parser 문장들 다 삭제해 주고 index.js 쪽에서 body-parser를 모듈로 안 불러오고 express로 불러옴<br>
==> 수정된 파일 : body-parser라는 단어 들어가는 모든 코드 삭제<br>
==> index.js 에서 body-parser모듈이 아닌 express모듈로 기능 불러오기 코드<br>
```
    app.use(express.json());
    app.use(express.urlencoded({extended: false}));
```
참고 : https://ninjaggobugi.tistory.com/11#:~:text=body%2Dparser%EB%8A%94%20%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4%EC%9D%B4%EB%8B%A4,%EB%A1%9C%20%ED%8C%8C%EC%8B%B1%ED%95%B4%EC%A3%BC%EB%8A%94%20%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4%EC%9D%B4%EB%8B%A4. <br>

npm start시 sh: 1: npm-run-all: Permission denied 에러 발생<br>
==> node_modules파일 삭제후 다시 npm install<br>

Nginx파일 수정후, 500 Internal Server Error가 발생<br>
==> build까지의 경로로 접근할 때 거치는 디렉토리들에 대해 외부에서의 실행 권한이 없어서 발생하는 문제일 가능성이 높으므로, 홈 디렉토리인 /home/ec2-user의 권한을 others의 실행 권한을 포함한 711로 설정한다.<br>
```
$ chmod 711 /home/ec2-user
```

pm2 안된 이유 <br>
==> https://mulder21c.github.io/2020/04/10/pm2-does-not-work-on-windows/<br>
==> startscript.js 파일을 따로 만들어주면 된다<br>

+ 뭣도 모르고 RDS db.m6g.large 사용했다가 하루에 요금 3만원 폭탄맞음 ㄷㄷ  <br>

Front쪽 URL 수정<br>
==> http://54.180.13.88/api/~~~~ <br>

글은 잘 안올라가나 Image가 올라가지 않음<br>
==> Front에 이미지가 http://54.180.13.88/uploads/이미지.jpg 형식으로 올라가게 수정<br>
==> 백엔드쪽 코드를 맞춰준다. <br>
        Server쪽 index.js에 app.use('/uploads', express.static('uploads')); 수정<br>
==> '/' 의 루트 location을 프론트 쪽에서 사용하고 있어서 충돌이 있었던 것 같다.<br>

하드코딩된 URL들 배포 버전으로 바꿔주기<br>
기존에는 axios.get("http:/localhost:3000/board") 이렇게 해서 API를 호출했다.<br>
AWS에 배포 후에는 주소가 달라졌으니 axios.get("http:/인스턴스퍼블릭IP/board")로 바꿔주었으나 실패 했었다.<br>
==> Nginx에서 proxy 설정을 해주어야 한다.<br>
==> 하란대로 했는데 에러가 뜬다. why?<br>
==> 주소의 경로 문제인 것 같다.<br>
==> location / 말고 location /api 를 이용하자. 이용을 위해서는 프론트 코드들도 "http:/퍼블릭IP/api/board" 이렇게 바꿔줘야 한다.<br>
==> 배포 할려면 const URL = process.env.NODE_ENV === 'production' ? '[aws인스턴스ip]' : 'http://localhost:3001' 선언하고 사용하라는데 잘 안되니 일단 하드코딩 했다.<br>

글은 잘 안올라가나 Image가 올라가지 않음<br>
==> Front에 이미지가 http://54.180.13.88/uploads/이미지.jpg 형식으로 올라가게 수정<br>
==> 백엔드쪽 코드를 맞춰준다. <br>
        Server쪽 index.js에 app.use('/uploads', express.static('uploads')); 수정<br>
==> '/' 의 루트 location을 프론트 쪽에서 사용하고 있어서 충돌이 있었던 것 같다.<br>
==> location /uploads 도 만들어준다.<br>

도메인 등록 후 다시 URL 수정<br>
const URL = process.env.NODE_ENV === 'production' ? '[도메인]' : 'http://localhost:3001' 얘를 다시 사용하자. 근데 안된다<br>
const URL = process.env.NODE_ENV === 'production' ? '' : 'http://localhost:3001' 이렇 해보자. <br>
axios.get(URL + "/api/board") 얘도 이런 식으로 바꿔준다. 된다. 들어오는 url값이 도메인만 들어온다. /api까진 안 들어오니 얘도 뒤에다가 같이 추가.<br>
얘는 코드찾아 삼만리라 시간이 좀 걸렸다.<br>
