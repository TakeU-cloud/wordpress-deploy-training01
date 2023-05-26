# Dockerについて
  docker compose up -dを実行すると、docker-compose.ymlの設定に従ってコンテナが生成される。
  この時、ymlファイルのvolumesに記載された内容は、別の領域に保持されるので、
  docker compose downを実行してもvolumesは削除されない。volumesも削除したい場合は、
  docker compose down -v

# WordPressの管理画面（ローカル開発環境）
  localhost:8000/wp-admin

# .gitignoreについて
  wp-content/languages
  wp-content/uploads
  上記のフォルダはgitignore対象にした方が良さそうです。

# AWS LightSailの注意点
  LightSailのRDSサービスは、MySQL ver5, 8系か、Postgresqlの２択なので、
  ローカル開発環境もMySQLでDockerイメージを作った方が良い。
  また、LightSailのRDSは、db nameに"db"という接頭辞が付くため、docker環境でもdatabase nameは
  "dbwordpress"のようにしておいた方が良い。

  LightSailでEC2インスタンスをWordPressセットで作成した場合、デフォルトでユーザー名とパスワードが設定される。
  user : user
  passowr : →LightSailのEC2ダッシュボードの「Connect using SSH」でターミナルを起動し、
            cat $HOME/bitnami_application_password
            このコマンドでpasswordが表示される。

# GitHub Actionsを使った自動デプロイ
  .github/workflows/deploy.ymlを作成してスクリプトを記述する。
  GiHubにsecurityパラメーターをセットして、deploy.ymlをpushするだけ。
  スクリプトは基本的にscpとcacheクリアで行う。

# LightSailのEC2インスタンスのWordPressから、LightSailのRDS（MySQL）に接続設定する
  RDSのNetworkingでpublicモードをONにする
  RDSのConnectでImportモードをONにする
  EC2のSSHターミナルを起動して、以下のコマンド
  sudo mysqldump -u root --databases bitnami_wordpress --single-transaction --compress --order-by-primary -p$(cat /home/bitnami/bitnami_application_password) | sudo mysql -u wordpress --host ls-324a24fa6ff327105d85cdc34d7f7e591edbff15.cswoev5ibaud.ap-northeast-1.rds.amazonaws.com --password
  →passwordはRDSのパスワードを入力する
  cp /opt/bitnami/wordpress/wp-config.php /opt/bitnami/wordpress/wp-config.php-backup
  vim /opt/bitnami/wordpress/wp-config.php
  →define( 'DB_USER', 'wordpress' );
  →define( 'DB_PASSWORD', 'wordpress' );
  →define( 'DB_HOST', 'ls-324a24fa6ff327105d85cdc34d7f7e591edbff15.cswoev5ibaud.ap-northeast-1.rds.amazonaws.com' );
  sudo /opt/bitnami/ctlscript.sh restart
  終わったら、RDSのConnectでImportモードをOFFに戻す

