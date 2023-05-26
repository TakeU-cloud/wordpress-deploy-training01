# Dockerについて
  docker compose up -dを実行すると、docker-compose.ymlの設定に従ってコンテナが生成される。
  この時、ymlファイルのvolumesに記載された内容は、別の領域に保持されるので、
  docker compose downを実行してもvolumesは削除されない。volumesも削除したい場合は、
  docker compose down -v

# WordPressの管理画面（ローカル開発環境）
  localhost:8000/wp-admin

# AWS LightSailの注意点
  LightSailのRDSサービスは、MySQL ver5, 8系か、Postgresqlの２択なので、
  ローカル開発環境もMySQLでDockerイメージを作った方が良い。
  また、LightSailのRDSは、db nameに"db"という接頭辞が付くため、docker環境でもdatabase nameは
  "dbwordpress"のようにしておいた方が良い。