Date: 2023. 01. 07. sat.

# :memo: 1. 순수 Express 사용 시 app 싱글톤 패턴 적용하기


싱글톤 패턴은 간단하게 말하면, 객체의 유일성을 보장하기 위해 오직 하나의 객체만을 사용할 수 있게하는 디자인 패턴이다. <br/>
싱글톤 패턴을 만들기 위해서는 보통 `class`와 접근제한자를 이용한다. 다음은 NODE이 웹 프레임워크인 Express를 사용할 때 발생할 수 있는 인스턴스 공유 문제를 해결하기 위해 싱글톤 패턴을 적용해보았다.

```js
import * as express from 'express';

const app = express();
```

<br/> 

express 이용 시 보통 가장 상위의 파일(app.ts / app.js / index.js ... )에서  `app` 이라는 변수로 인스턴스를 만들어서 사용한다. <br/>
하지만 이 `app` 은  전역에서 사용 가능한 인스턴스다. 외부에서 접근을 제한하고, 오직 가장 상위 모듈에서만 생성할 수 있도록 싱글톤 패턴으로 작성해야 한다.

```ts
// 기존의 인스턴스
const app: express.Application = express();

// 클래스화

class Server {
    public app: express.Application;

    constructor() {
        this.app = express(); // 생성자를 통해 필드변수 app에 express 객체를 할당
    };

    // 옵션: Route들을 모아놓은 메소드
    private setRoute() {
        this.app.use(userRouter);
        this.app.use(authRouter);
        this.app.use(menuRouter);
    };

    // 옵션: 미들웨어들을 모아놓은 메소드
    private setMiddleware() {
        this.app.use((req: this.app.Request, res: this.app.Response, next: this.app.NextFunction) => {
            // 로그인 판별 미들웨어
            // 404 미들웨어 ...
        });

        this.app.use(express.json());
        this.setRoute(); // 상단의 setRoute 사용
    };

    // 포트 옵션
    public static listen() {
        this.setMiddleware();
        this.app.listen(8000); // env 파일로 빼도 됨
    };
};


// 싱글톤 패턴으로 구성한 서버 사용

function init() {
    const server = new Server();
    server.listen(); 
}

init();

```