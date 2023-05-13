## [ハンズオン準備]　Argo CDのインストール

### p22

#### kindをインストール
```
brew install kind
```

#### kindのクラスタを作成する
```
kind create cluster
```

### p23
#### これらのコマンドはArgo CDのドキュメントを参照しています: https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

```
kubectl get pods -n argocd
```

## [ハンズオン] Argo CD　を使ってアプリをデプロイしてみよう

### p24

#### port-forwardを実施
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

#### Argo CDのイニシャルパスワードを取得する
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### p25
#### argocd CLI をインストール
````
brew install argocd
````

#### argocd-server にログインする
```
argocd login localhost:8080
```

### p26
#### context を切り替える
```
kubectl config set-context --current --namespace=argocd
```

#### Argo CD のApplicationを作成する
```
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
```

### p27
#### Podが作成されていないことを確認する
````
kubectl get pod -n default
````

#### Application をsyncする
```
argocd app sync guestbook
```

## [ハンズオン] git push で GitOps を体験しよう

### p29
#### リポジトリURL
```
https://github.com/aoi1/argocd-repo-sample
```

### p30
#### Application リソースを作成する
```
cd <cloneしたリポジトリ>
kubectl apply -f applications/develop/nginx.yaml
```

### p31
#### git command
```
git switch -c add-service
git add manifests/develop/service.yaml
git push -u origin add-service
```

### p33
#### staging/production用Applicationを作成する
```
kubectl apply -f applications/staging/nginx.yaml
kubectl apply -f applications/production/nginx.yaml
```

### p35
#### Deploymentを削除する
```
kubectl delete deployment nginx-deployment -n staging
```
