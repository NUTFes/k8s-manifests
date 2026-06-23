この README は、k8s-manifests/seeft/ ディレクトリで実行する前提です。

# 手動での立ち上げ方
マニフェストの適用による立ち上げは、以下の手順で行ってください。
エラーの治し方については、Notionのk8sセットアップ6章を参考にしてください。

- `kubectl apply -f namespace.yml`
- env, secrets作成（以下READMEを参照）
- `kubectl apply -f deployment.yml`
- `kubectl apply -f service.yml`

# env, secrets
環境変数や認証情報を含むシークレットの作成は、GitHubには含めません。手動で行い作成してください。
必要なファイルに関しては、settingsリポジトリや、Notionを参照してください。

## harbor-creds
HarborからDocker Imageをpullするためのpuller認証用Secret。
`--docker-password`に関しては、InfraページのNotionを確認してください。
```bash
kubectl create secret docker-registry harbor-creds \
  -n seeft \
  --docker-server='reg.nutmeg.cloud' \
  --docker-username='robot$pull' \
  --docker-password="<slack参照>" \
  --docker-email='ignore@example.com' \
  --dry-run=client -o yaml \
  | kubectl apply -f -
```

## cloudflare-configs
cloudflaredの設定で必要な `cert.pem`, `<tunnel-id>.json`, `config.yml` を入れたディレクトリを、そのままSecretに設定する。
settingsリポジトリを参照してください。
```bash
kubectl create secret generic cloudflare-configs \
  -n seeft \
  --from-file=./path/to/cloudflare/files \
  --dry-run=client -o yaml \
  | kubectl apply -f -
```

## seeft-api-env
env_file: ["./api/env/seeft.env"] に相当するAPI用の環境変数Secret。
settingsリポジトリを参照してください。
```bash
kubectl create secret generic seeft-api-env \
  -n seeft \
  --from-env-file=./path/to/seeft.env \
  --dry-run=client -o yaml \
  | kubectl apply -f -
```

## 入っているかの確認
```
kubectl -n seeft get secret
```
