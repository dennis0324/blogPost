---
{
"title":"typscript webpack electron으로 애플리케이션 만들기",
"tags":["html", "typescript", "design", "studying","webpack"]
}
---

# Electron 앱 제작
   
--//
..first link{+first+}
..second link{+second+}
..third link{+third+}
//--

## 2022-04-02
{+first+}오랜만에 포스팅을 하긴 하는데 대학교 수업이 2학년이 들어서고 생각보다 많아서 백준 사이트 코딩과 여러가지를 꾸준히 해오긴 했는데 보여줄게 없다... 


사실 홈페이지 제작은 기숙사에 들어오면서 모니터 해상도가 달라서 디자인을 하기 힘들어 일단 잠정 보류로 냅뒀다. 그래서 백준을 열심히 한 것 같다.

그리고 백준만 하다보니 너무 힘들어서 뭐라도 만들어야겠다는 생각으로 만들기 시작했다.

남자들이라면 모를리가 없는 꺼토미 히라를 크롤링해서 데이터를 앱으로 제작해보려 한다.
크롤링 노드는 이미 다 만들었다. 이 포스팅의 주요 이유는 바로 webpack typescript electron 의 애플리케이션 제작과 개발 환경 제작이다. 
나름 이것도 꽤 오랜 시간을 공들여 가면서 만들었다.

크롤링 관련 node는 추후에 설명하기로 하자.

들어가기에 앞서 이 글의 제작은 필자의 공부 목적으로 작성되었지만 필자도 [이거]보면서 제작했다.
필자가 쓴 글이 이해가 되지 않는다면 들어가서 보면서 하는 것도 방법이다. 하지만 여기서는 ts가 아닌 js를 사용하기 때문에 그 차이점으로 인해서 추가 기록하는 느낌이라고 생각하면 될 것 같다.

기본적인 틀은 npm init으로 시작하였다.
시작하기 전에 주요 node module들 다 대충 훑고 넘어가보도록 하겠다.

```
    "@babel/core": "^7.17.8",
    "@babel/preset-env": "^7.16.11",
    "@babel/preset-react": "^7.16.7",
    "@types/react": "^17.0.43",
    "@types/react-dom": "^17.0.14",
    "babel-loader": "^8.2.4",
    "css-loader": "^6.7.1",
    "electron-reload": "^2.0.0-alpha.1",
    "electron-reloader": "^1.2.3",
    "sass": "^1.49.11",
    "sass-loader": "^12.6.0",
    "style-loader": "^3.3.1",
    "typescript": "^4.6.3",
    "webpack": "^5.71.0",
    "webpack-cli": "^4.9.2"
```

2022.04.03일 기준으로 이렇게 일단 있다. @types는 타입스크립트에서 사용할 수 있도록 해주는 노드 모듈이라고 생각하면된다.

우선
```
npm init -y
```
실행하여 package.json을 초기화 시켜준다.

```shell
npm install --save-dev electron electron-reload typescript
```

이렇게 기본적인 것만 실행해서 다운 받아준다.

```
  "scripts": {
    "ts-c": "cd ./ts & tsc -w",
    "watch": "webpack --config webpack.common.js --watch",
    "start": "set NODE_ENV=development&&electron .",
    "dev": "npm run ts-c & npm run watch & npm start"
  },
```

그 후 package.json에서 스크립트 부분을 살짝 고쳐준다. 사실 추후에 고쳐야 될지 모르겠지만 일단 이렇게 만들었다.

그리고 내가 제작할 애플리케이션 파일 구조는 이렇게 생겼다.

```
RootFile
│  index.html
│  package-lock.json
│  package.json
│  webpack.common.js
│
├─.vscode
│      tasks.json
│
├─build
│  └─js
│          app.js
│
├─js
│  ├─electron
│  │      main.js
│  │      preload.js
│  │
│  └─react
│          app.js
│          index.js
│          index.scss
│
└─ts
    │  tsconfig.json
    │
    ├─electron
    │      main.ts
    │      preload.ts
    │
    └─react
            app.tsx
            index.tsx
```

위 파일 구조에서 볼 수 있듯이 electron의 실행 파일은 js/electron/main.js에 존재한다. 따라서 package.json에서 실행 파일의 위치를 변경해주어야한다.
```
  "main": "./js/electron/main.js",
```

그 후 ts폴더 안에 tsconfig.json을 생성해서 정의해주었다.(tsconfig json의 속성값을 알고 싶다면 tsc --init하면 기본 주석처리된 모든 속성값이 나오므로 확인하면 된다.)

```json
{
  "compilerOptions": {
    "jsx": "react",                                
    "module": "commonjs",                                
    "esModuleInterop": true,                             
    "rootDir": "./",
    "outDir": "../js"
  }
}
```
이 config에서 제일로 중요한 곳은 `"jsx":"react"`이다.  
저거 없으면 추후에 넣을 react가 안 돌아가므로 꼭 넣어야한다.  

`rootdir`는 그 tsconfig 파일이 컴파일할때 어느 부분의 폴더부터 컴파일했으면 좋겟는지 알려주는 속성값이고

`outDir`는 그 컴파일한 파일들을 저장 시키게 해주는 폴더 위치라고 생각하면된다.

main.ts를 만들기 전에 우선 기본적인 창을 띄워줄 html를 만들어줘야한다.
아는 사람도 있고 모르는 사람도 있겠지만 electron의 UI는 모두 html로 설계되고 제작되기 때문에 처음 인덱스가 필요하다.

그리고 가장 중요한건 electron는 single page application이다. 따라서 html를 여러개를 넣을 순 있지만 권장하지 않는다.


```html
<!--index.html-->

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8"/>
    <meta http-equiv="Content-Security-Policy" content="script-src 'self'"/>
  </head>
  <body>
    <div id="root">
    </div>
    <script src="./build/js/app.js"></script>
  </body>
</html>
```
사실 이 파일에는 많이 뭐가 들어가지 않는다. 애초에 이 index.html는 우리가 사용할 react와 electron를 연결해줄 매개체 밖에 되지 않기에 
많이 들어가지 않는다.
그리고 node js의 모듈을 기본적으로 사용하기 때문에 보안의 위험이 굉장히 큰 편이다. [sercutiy of electron]

따라서 안에 들어가있는 스크립트만 사용할 수있도록 CSP를 걸어 놓는다. 
이는 electron가 권장하는 방법이라고 한다.

마지막 스크립트 태그를 보면 알겠지만 app.ts가 app.js로 변환되어서 build폴더 안에 넣어지게 된다.

이제  ts/electron 파일에 가서 실행할 파일을 만들어주자.

```typescript
const { BrowserWindow,app } = require('electron'); 
const path = require('path');
const env = process.env.NODE_ENV || 'development';
```

필요 노드다 3번째 줄은 개발환경에서 실했을때 추후에 추가할 hot reload를 위한 줄이다.


```
function createWindow() {
  const win = new BrowserWindow({
    width: 1200,
    height: 800,
    backgroundCoor: "white",
    webPreferences: {
      NodeIterator: false,
      worldSafeExecuteJavascript : true,
      contextIsolation: true,
      preload : path.resolve(path.join(__dirname, "preload.js"))
    }
  })

  win.loadFile('index.html');
  
}
```
아무 것도 없이 electron을 실행했을 때는 아무 것도 안 뜨기 때문에 창을 하나 만들어줘야한다.

창을 만들고 그 창에다가는 index.html를 넣으면 된다.


```typescript
if (env === 'development') {
  require('electron-reload')(path.join(__dirname,'..','..'), {
      electron: path.join(__dirname,'..','..','node_modules','electron')
  });
}
```

그렇게 되면 전체 코드는 이렇게 된다.

```typescript
const { BrowserWindow,app } = require('electron'); 
const path = require('path');
const env = process.env.NODE_ENV || 'development';

function createWindow() {
  const win = new BrowserWindow({
    width: 1200,
    height: 800,
    backgroundCoor: "white",
    webPreferences: {
      NodeIterator: false,
      worldSafeExecuteJavascript : true,
      contextIsolation: true,
      preload : path.resolve(path.join(__dirname, "preload.js"))
    }
  })

  win.loadFile('index.html');
  
}

// If development environment 
if (env === 'development') {
  require('electron-reload')(path.join(__dirname,'..','..'), {
      electron: path.join(__dirname,'..','..','node_modules','electron')
  });
}
app.whenReady().then(createWindow)
```

그리고 개발모드에서만 실행되는 hot reload는 electron-reload이라는 모듈을 이용하게 되는데 현재 main.ts가 있는 위치가 최상위 폴더가 아니므로 꼭 주의해야한다.
node.js에서 기본적으로 제공해주는 `__dirname`은 현재 코드가 실행되는 위치이므로 ts/electron이 되어 버리기에 꼭 폴더 위치를 재조정해줘야 한다.
아니면 실행할 수없는 파일이라고 뜨며 파일이 실행되지 않는다.

이제 react를 설정할 것이다. 위에 이미 다운 받은 것들을 제외하고 나머지 것들을 다 다운받아준다.

```
npm install --save-dev sass sass-loader style-loader @babel/core @babel/preset-env @babel/preset-react @types/react @types/react-dom babel-loader css-loader webpack webpack-cli
```

많다... 그래도 다 쓰니깐 넣어 놓도록 하자

react를 제작하기 전에 웹팩 설정 파일을 간단히 만들고 갈 것이다.

```javascript
//webpack.common.js

const path = require('path');

module.exports = {
  mode: 'development',
  entry: './js/react/index.js',
  // TODO: Explain Source Map
  devtool: 'inline-source-map',
  target: 'electron-renderer',
  // plugins: [
  //   new webpack.ProvidePlugin({
  //      "React": "react",
  //   }),
  // ],
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [[
              '@babel/preset-env', {
                targets: {
                  esmodules: true
                }
              }],
              '@babel/preset-react']
          }
        }
      },
      {
        test: [/\.s[ac]ss$/i, /\.css$/i],
        use: [
          // Creates `style` nodes from JS strings
          'style-loader',
          // Translates CSS into CommonJS
          'css-loader',
          // Compiles Sass to CSS
          'sass-loader',
        ],
      }
    ]
  },
  resolve: {
    extensions: ['.js'],
  },
  output: {
    filename: 'app.js',
    path: path.resolve(__dirname, 'build', 'js'),
  },
};

```
앞서 알려주었던 동영상에서 동영상 젝자의 깃허브에 있는 것을 복사 해왔다. 거기서 몇가지만 지웠는데 사용하지 않을 플러그인과 모듀을 지워 주었다.그리고 위쪽 코드에서 `entry`의 코드만 우리가 현재 사용하고 있는 main.js의 위치로 바꿔주면 된다.
그리고 빌드 된 후 나온 파일은 코드 아래쪽에 있는 `output`에 들어가게 된다.

마지막으로 react의 설계다. react를 해보았거나 react를 잘 아는 사람들은 쉬울 수도 있다.
```node
import React from 'react';
import ReactDOM from 'react-dom';
import ReactDOMClient from 'react-dom/client';


import App from './app'
import './index.scss';

const rootElement = document.getElementById("root")
const root = ReactDOMClient.createRoot(rootElement)
root.render(<App/>)
```
그냥 간단한 인덱스이다. 여기서 주의해야 할 것은 `ReactDOM.render()`가 raect가 react18 업데이트 되면서 지원이 중지가 된다고 하여서 
`ReactDOMClient.createRoot`를 사용해 주었다. 자세한 사항은 [Replacing render with createRoot]를 참조하면 좋을 것 같다.

그래서 작동과 환경 제작의 목적이므로 아무것도 넣지 않고 그냥 App만 넣어서 정의해주었다.


```node
import React from "react";
export default function App(){
  return (
  <>
    <h1>I am App Component</h1>
    <button onClick={() => {
      // @ts-expect-error
      electron.notificationApi.sendNotification("my custom notification")
    }}>Notify</button>
  </>
  )
}

```
그 앱에 관한 코드이다. 역시 아무것도 없다. 하지만 알아야하는 곳이 있는데 현재 html과 그 관한 js코드들은 인증 받지 못한 곳에서 들어온 코드라면 실행이 불가능하게 설계되어있다.
그렇다면 데이터를 react와 electron끼리 서로 주고 받을 때 어떻게 해야되는지 모를 것이다. 그게 바로 `electron.notificationApi` 여기서 볼 수 있는데 electron에 preload라는 속성값이 있어서 거기서 값을 보내거나 받아 올 수 있다.

하지만 typescript에서는 글로벌에 변수로 선언해서 값을 받아 올 수 있다 하여 선언을 받지 않았기에 error을 출력하게 되는데 이때 `// @ts-expect-error`를 사용해줌으로써 error가 날 필요가 없고 값이 들어오는 곳이라고 지정해줄 수 있다.


```typescript
//electron/preload.ts
const { ipcRenderer, contextBridge} = require('electron');

contextBridge.exposeInMainWorld('electron',{
  notificationApi : {
    sendNotification(message){
      console.log(message)
      ipcRenderer.send('notify',message);
    }
  }
})
```
preload의 기본 형태를 잡아주기 위해서 간단한 명령어만 넣었다. `contextBridge.exposeInMainWorld` 말그대로 모든 스크립트에서 전역을 사용할 수 있도록 만들어주는 방식이다.
전역변수의 이름을 electron으로 설정해주는 것이다. 그리고 그 `electron`안에는 notificationApi라는 Api가 있다. 이 방식을 사용해서 react에서 상호작용시에 데이터를 보내거나 받을 수 있게 되는 것이다.


이렇게 짜면 빌드는 충분히 되지만 더 낫고 편안한 환경을 만들기 위해서 추가적으로 작업을 한게 있다.

vscode 자체에서 지원해주는 `tasks.json`과 extension으로 react 컴포넌트 관리이다.
우선 `F1`을 눌러서 실행 설정들을 열서 tasks 검색후 
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/makingElectronApp/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(134).png?raw=true)
를 눌러서

감지 작업 중 npm:start를 눌러서 tasks.json를 제작해준다.
사실 그냥 .vscode 만들어서 tasks.json를 만들어줘도 된다.

```json
{
	"version": "2.0.0",
	"tasks": [
		{
			"label": "test App",
			"dependsOn": [
				"npm: watch",
				"npm: ts-c",
				"npm: start"
			]
		},
		{
			"type": "shell",
			"label": "npm: ts-c",
			"command": "npm run ts-c",
			"presentation": {
				"group": "app test component",
      }
		},
		{
			"type": "shell",
			"label": "npm: watch",
			"command": "npm run watch",
			"presentation": {
				"group": "app test component",
      }
		},
		
		{
			"type": "shell",
			"label": "npm: start",
			"command": "Start-Sleep -s 5 | npm start",
			"presentation": {
				"group": "app test component",
      }
		}
	]
}

```

이게 json 설정이다. 빌드를 위해서는 3개의 멸령어가 실행 되어야 한다. typescript에서 javascript으로 변환해주는 컴파일러 그리고 react를 script태그에다가 넣어줄 수 있게 바꿔주는 동작, 마지막으로 electron의 변경마다 hot reload를 시켜주는 동작으로 총 3개인데 하나 씩 실행 시키기 귀찮아서 묶어버렸다.

```json
{
	"label": "test App",
	"dependsOn": [
		"npm: watch",
		"npm: ts-c",
		"npm: start"
	]
},
```
이건 별거 아니고 그냥 3개의 명령어를 실행 시키기 위해서 한꺼번에 실행을 도와주는 시작이다.
label은 필자가 한것처럼 안하고 다른 이름으로 작성해도된다. dependsOn은 이름을 바꿀 수 있지만 나머지 tasks들의 label과 이름을 맞춰야한다.

```json
{
	"type": "shell",
	"label": "npm: ts-c",
	"command": "npm run ts-c",
	"presentation": {
		"group": "app test component",
	}
}
      
```
타입스크립트의 컴파일이다.
타비스크립트를 작성하고 저장할때마다 지속적으로 업데이트 해준다.

```json
		{
			"type": "shell",
			"label": "npm: watch",
			"command": "npm run watch",
			"presentation": {
				"group": "app test component",
      }
```
react의 변경이 확인되면 실행해서 다시 업데이트 해주는 동작을 해주는 프로세스를 실행해준다.

```json
{
	"type": "shell",
	"label": "npm: start",
	"command": "Start-Sleep -s 5 | npm start",
	"presentation": {
		"group": "app test component",
      	}
}
```


마지막으로 electron 그 자체를 실행시켜주는 tasks이다. 
하지만 먼저 실행되면 react가 로드 되지 않은 상태로 샐행되기 때문에 Start-Sleep를 통해서 몇 초 쉰다음에 실행해주도록 만들어주었다.

이렇게 하면 이제는 `F1` > `tasks: run tasks` > `start with no scan`이렇게 하면 자동으로 실행해준다.
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/makingElectronApp/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7(135).png?raw=true)

실행하면 이렇게 3개가 동시에 보인다!

마지막 필요한 vscode extension는 `review.js`다.

메인 인덱스에 넣었다가 뺐다 하는 번거로움을 없애기 위해서 사용한다. 
자세한 내용은 필자보다 [사이트]가 잘 해놨으니깐 확인해보길 바란다.


[이거]:https://www.youtube.com/watch?v=VCl8li22mrA
[sercutiy of electron]:https://www.electronjs.org/docs/latest/tutorial/security
[Replacing render with createRoot]:https://github.com/reactwg/react-18/discussions/5
[사이트]:https://previewjs.com/
