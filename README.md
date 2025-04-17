# minikube
0417exam

AWS上新增執行個體，之後再VSCODE開REMOTE遠端到虛擬機
新增要上傳docker檔案，之後在跟目錄下用terminal執行 npm init，得到package.json

目錄會長以下這樣：
mygo/
├── html/
│   ├── index.html
│   └── style.css
├── nginx.conf
├── Dockerfile
├── package.conf
└── package-lock.conf
│
k8s/
├── Deployment.yaml
└── Service.yaml

----------- coding集 --------------
#### package.conf ####
{
  "name": "minikube-main",
  "version": "1.0.0",
  "description": "0417exam",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^5.1.0"
  }
}

####index.html####
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>我的學號</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="container">
    <h1>M11307003</h1>
    <p class="student-id">oreo</p>
  </div>
</body>
</html>


#### style.css ####
/* 頁面背景和字型基本設定 */
body {
    margin: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: linear-gradient(to right, #e0eafc, #cfdef3);
  }
  
  /* 置中容器樣式 */
  .container {
    background-color: #ffffff;
    padding: 40px;
    border-radius: 12px;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
    text-align: center;
    max-width: 90%;
    width: 400px;
  }
  
  /* 標題樣式 */
  h1 {
    margin-bottom: 20px;
    color: #333333;
  }
  
  /* 學號樣式（重點強調） */
  .student-id {
    font-size: 2em;
    color: #1e90ff;
    font-weight: bold;
    background-color: #f0f8ff;
    padding: 10px 20px;
    border-radius: 8px;
    box-shadow: 0 4px 10px rgba(30, 144, 255, 0.2);
  }
  
  /* 響應式設計：小螢幕調整 */
  @media (max-width: 480px) {
    .container {
      padding: 20px;
    }
  
    .student-id {
      font-size: 1.5em;
      padding: 8px 16px;
    }
  }  

#### Dockerfile ####
FROM nginx:alpine

# 拷貝自定義設定
COPY nginx.conf /etc/nginx/nginx.conf

# 拷貝網頁到 nginx 的根目錄
COPY html/ /usr/share/nginx/html/

#### nginx.conf ####
events {}

http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            index index.html;
        }
    }
}

----------- coding集畢 -------------

coding完成後，接下來要上傳到dockerhub，把它包成一個Docker Image

docker login
->GOOGLE登入
docker build .
docker tag imageID matchaor3o/mygo:v1
docker push matchaor3o/mygo:v1


上傳成功後，接著開始minikube部屬，建立2個yaml檔分別一個為Deployment.yaml和Service.yaml：

----------- coding集 -------------

#### Deployment.yaml ####

apiVersion: apps/v1
kind: Deployment
metadata:
  name: student-id-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: student-id-app
  template:
    metadata:
      labels:
        app: student-id-app
    spec:
      containers:
      - name: student-id-container
        image: matchaor3o/mygo
        ports:
        - containerPort: 80


#### Service.yaml ####
apiVersion: v1
kind: Service
metadata:
  name: student-id-service
spec:
  type: NodePort
  selector:
    app: student-id-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30036

----------- coding集畢 -------------

之後要在terminal裡使用kutectl部屬：

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get pods //查看部屬是否成功
kubectl get svc

使用 Minikube 打開服務

minikube service student-id-service







  
