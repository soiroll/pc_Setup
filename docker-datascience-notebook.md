
## 事前準備
- 共有したGoogleドライブのフォルダをDLとzip解凍
- WSL上で `mkdir -p /home/$USER/workspace/` を実行

## Windows側
1. エクスプローラーのアドレスバーに `\\wsl$` を入力
2. \\wsl.localhost\Ubuntu-xxxx\home\$USER\workspase 配下にファイル構成で設置

```
.devcontainer/
.ipynb_checkpoints/
Dockerfile
Manifest.toml
Project.toml
Untitled.ipynb
docker-compose.yml
extent/
```

## WSL側
```
cd /home/$USER/workspace/docker-datascience-notebook

# Docker イメージ作成 （時間がすごくかかる）
docker build -t datascience-notebook:251120 -f Dockerfile .

# 作成したDocker イメージ一覧（出力例）
docker images
-------
REPOSITORY              TAG          IMAGE ID       CREATED         SIZE
datascience-notebook    251120       xxxxxxx        7 weeks ago     8.72GB

# Dockerコンテナ起動
docker compose -f docker-compose.yml -f .devcontainer/docker-compose.yml up -d

# Dockerコンテナ停止
docker compose down
```

## Dockerコンテナ内にVscode上から接続
<img width="879" height="976" alt="vscode_attach-container" src="https://github.com/user-attachments/assets/f216d1fc-8617-475f-9e07-55612cd9caa5" />
