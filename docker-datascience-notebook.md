
## 事前準備
- 共有したGoogleドライブのフォルダをDLとzip解凍
- WSL上で `mkdir -p /home/$USER/workspace/` を実行
- WSL のユーザーを docker グループへ追加(今まで行っていない場合)
`sudo usermod -aG docker $USER`


## Windows側
1. エクスプローラーのアドレスバーに `\\wsl$` を入力
2. `\\wsl.localhost\Ubuntu-xxxx\home\$USER\workspase` 配下に docker-datascience-notebook フォルダを移動

docker-datascience-notebook 配下の構成
```
.devcontainer/
Dockerfile
Project.toml
Untitled.ipynb
docker-compose.yml
extent/
```

## WSL側

1. docker-datascience-notebook フォルダに移動  
`cd /home/$USER/workspace/docker-datascience-notebook`
  
2. Manifest.toml を削除し、Dockerfile 39, 40行目を以下のように編集し、ctrl + S で保存
```
# COPY --chown=${NB_USER}:${NB_GID} Project.toml Manifest.toml /opt/julia_env/
COPY --chown=${NB_USER}:${NB_GID} Project.toml /opt/julia_env/
```
  
3. Docker イメージ作成 （時間がすごくかかる）
```
docker build --no-cache -t datascience-notebook:251120 -f Dockerfile .

# 作成したDocker イメージ一覧（出力例）
docker images
-------
REPOSITORY              TAG          IMAGE ID       CREATED         SIZE
datascience-notebook    251120       xxxxxxx        7 weeks ago     8.72GB
```

4. docker-compose.yaml の修正 (コメントアウトする)
```
#- ./julia_depot:/opt/julia_depot 
```

5. Dockerコンテナ起動
```
docker compose -f docker-compose.yml -f .devcontainer/docker-compose.yml up -d

- 例
[+] Running 2/2
 ✔ Network datascience-notebook_default      Created                                                                                         0.1s 
 ✔ Container datascience-notebook-jupyter-1  Started                                                                                         0.4s 
```

ex) Dockerコンテナ停止
```
docker compose down

- 例
[+] Running 2/2
 ✔ Container datascience-notebook-jupyter-1  Removed                                                                                         0.7s 
 ✔ Network datascience-notebook_default      Removed 
```


### Dockerコンテナ内にVscode上から接続
<img width="879" height="976" alt="vscode_attach-container" src="https://github.com/user-attachments/assets/f216d1fc-8617-475f-9e07-55612cd9caa5" />


## Julia パッケージの永続化
### コンテナ内で
```
mkdir /home/jovyan/work/julia_depot
(cd /opt/julia_depot && tar cf - .) | (cd /home/jovyan/work/julia_depot && tar xvf -)
```

### WSL側
1. `docker compose down` で一度コンテナを停止
2. docker-compose.yaml の修正 (コメントアウトを外す)
```
- ./julia_depot:/opt/julia_depot 
```

3. 再度コンテナ起動
```
docker compose -f docker-compose.yml -f .devcontainer/docker-compose.yml up -d
```


