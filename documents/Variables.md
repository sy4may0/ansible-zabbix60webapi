# ZabbbixAPIログイン設定
ZabbixAPIへのログイン設定を定義します。
```yml
# ログインユーザー名
zabbix_webapi_user: Admin

# ログインパスワード
zabbix_webapi_pass: zabbix

# ZabbixサーバーのIPアドレスまたはホスト名
zabbix_webapi_host: 127.0.0.1

# ZabbixのURLパス
# http://localhost/zabbixであれば、`zabbix`を指定する。
zabbix_webapi_url_path: zabbix

# ZabbixのWebアクセスポート
# http://localhost:8080/zabbixであれば、8080を指定する。
zabbix_webapi_httpapi_port: 80

# ZabbixのWebアクセス時、HTTPSで接続する。
zabbix_webapi_httpapi_use_ssl: false

# ZabbixのWebアクセス時、証明書のチェックを行う。
zabbix_webapi_httpapi_validate_certs: false
```

# Zabbixテンプレートのインポート設定
Zabbixのテンプレート設定を格納したディレクトリを定義します。
```yml
zabbix_webapi_template_json_dir: "{{ inventory_dir }}//zabbix_templates/"
```

ここで指定したディレクトリにZabbixテンプレートのエクスポートファイル(**JSON形式**)を格納しておくと、Ansible実行時にテンプレートをZabbixにインポートします。

# Zabbixのホストグループ設定
Zabbixのホストグループ設定を定義します。

ホストグループ設定は以下の形式で定義します。
```yml
zabbix_webapi_hostgrops:
  - state: 作成/削除(present|absent)
    groups: 作成するホストグループ名のリスト
```

作成するホストグループは`state: present`としてデータを定義します。削除するホストグループは`state: absent`としてデータを定義します。

以下はホストグループを設定する定義例です。
```yml
zabbix_webapi_hostgroups:
  - state: present
    groups:
      - web-server
      - app-server
      - db-server
  - state: present
    groups:
      - monitoring-server
  - state: absent
    groups:
      - test-server
```
この例では以下のホストグループを作成します。

- web-server
- app-server
- db-server
- monitoring-server

また、以下のホストグループを削除します。

- test-server

# Zabbixのホスト設定
Zabbixのホスト設定を定義します。ホスト設定はパラメーターを定義した辞書型のリストを定義します。

ホスト設定は以下の形式で定義します。
```yml
zabbix_webapi_hosts:
  - state: 作成/削除(present|absent)
    status: ホストのステータス(enabled:有効|disabled:無効)
    host_name: ホスト名
    visible_name: 表示名
    description: ホストの説明
    host_groups:　所属させるホストグループ(List型)
    link_templates: リンクするテンプレート(List型)
    interfaces: インターフェース設定(List型)
    macros: マクロ設定(List型)
    tags: タグ設定(List型)
```

`host_groups`はホストグループ名を格納したList型で定義します。

`link_templates`はテンプレート名を格納したList型で定義します。

`interfaces`は以下の辞書型を格納したList型で定義します。
```yml
type: インターフェースタイプ(1:Agent|2:SNMP)
main: (1:メインインターフェース|0:サブインターフェース)
useip: (1:IPを使用する|0:DNSを使用する)
ip: IPアドレス
dns: DNS名
port: ポート番号
# SNMPインターフェースの場合、detailsを追加で定義する。
details:
  version: SNMPのバージョン(1:v1,2:v2c,3:v3)
  community: SNMPコミュニティ名
```

`macros`は以下の辞書型を格納したList型で定義します。

```yml
macro: マクロ名
value: マクロの値
```

`tags`は以下の辞書型を格納したList型で定義します。
```yml
tag: タグ名
value: タグの値
```

以下はホストを1つ作成する定義例です。
```yml
zabbix_webapi_hosts:
  - state: present
    status: enabled
    host_name: ExampleHost
    visible_name: ExampleHost
    description: ''
    host_groups:
      - 'Zabbix servers'
    link_templates:
      - 'Template Module ICMP Ping'
    interfaces:
      - type: 1
        main: 1
        useip: 1
        ip: 192.168.11.1
        dns: ''
        port: '10050'
      - type: 2
        main: 1
        useip: 1
        ip: 192.168.11.1
        dns: ''
        port: '161'
        details:
          version: 2
          community: '{$SNMP_COMMUNITY}'
    macros:
      - macro: '{$CPU_USAGE_MAX}'
        value: 90
    tags:
      - tag: Takahiro
        value: karasawa
```

# メディアタイプ設定(Email)
Email型のZabbixメディアタイプ設定を定義します。メディアタイプ設定はパラメーターを定義した辞書型のリストを定義します。

メディアタイプ設定は以下の形式で定義します。
```yml
zabbix_webapi_mediatypes:
  - name: メディアタイプ名
    smtp_server: SMTPサーバー
    smtp_server_port: SMTPポート 
    smtp_helo: SMTP_HELO
    smtp_email: メールのFROMアドレス
    status: メディアタイプのステータス(enabled:有効|disabled:無効)
    state: 作成/削除(present|absent)
```

以下はメールを送信するメディアタイプを1つ作成する定義例です。
```yml
zabbix_webapi_mediatypes:
  - name: Email
    smtp_server: 'localhost'
    smtp_server_port: 25
    smtp_helo: 'localhost'
    smtp_email: 'zabbix@localhost.localdomain'
    status: enabled
    state: present
```

# メディアタイプ設定(スクリプト)
スクリプト型のZabbixメディアタイプ設定を定義します。メディアタイプ設定はパラメーターを定義した辞書型のリストを定義します。

メディアタイプ設定は以下の形式で定義します。
```yml
zabbix_webapi_scripts:
  - name: メディアタイプ名
    script_name: スクリプトファイル名
    script_params: スクリプトの引数パラメータのリスト
    status: メディアタイプのステータス(enabled:有効|disabled:無効)
    state: 作成/削除(present|absent)
```

`script_params`はスクリプトの引数を格納したList型で定義します。

以下は`tamanegi.sh`を実行するスクリプトを作成する定義例です。

```yml
zabbix_webapi_scripts:
  - name: script
    script_name: tamanegi.sh
    script_params: 
      - '{ALERT.SENDTO}'
      - '{ALERT.SUBJECT}'
      - '{ALERT.MESSAGE}'
    status: enabled
    state: present
```

このスクリプトは以下の通り実行されます。
```bash
./tamanegi.sh {ALERT.SENDTO} {ALERT.SUBJECT} {ALERT.MESSAGE}
# {}の値はZabbixのイベントに従って適切な値が代入されます。
```

# ユーザーグループ設定
Zabbixユーザーグループ設定を定義します。ユーザーグループ設定はパラメーターを定義した辞書型のリストを定義します。

ユーザーグループ設定は以下の形式で定義します。
```yml
zabbix_webapi_usergroups:
  - status: メディアタイプのステータス(enabled:有効|disabled:無効)
    state: 作成/削除(present|absent)
    name: ユーザーグループ名
    rights: グループに割り当てる権限のリスト
    tag_filters: タグフィルターのリスト 
```

`rights`は以下の形式のリスト型を定義します。
```yml
- host_group: ホストグループ
  permission: パーミッション(denied|read-only|read-write)
```

`tag_filters`は以下の形式のリスト型を定義します。
```yml
- host_group: ホストグループ
  tag: タグ名
  value: タグの値
```

また、タグフィルターを使用しない場合、`tag_filters`に空のリストを定義します。
```yml
tag_filters: []
```

以下は`ACME`ユーザーグループを設定する定義例です。
```yml
zabbix_webapi_usergroups:
  - state: present
    status: enabled
    name: ACME
    rights:
      - host_group: Templates
        permission: read-only
    tag_filters: []
```

# ユーザー設定
Zabbixのユーザーアカウント設定を定義します。ユーザー設定はパラメーターを定義した辞書型のリストを定義します。

ユーザー設定は以下の形式で定義します。
```yml
zabbix_webapi_users: 
  - state: 作成/削除(present|absent)
    username: ユーザーアカウント名
    name: ユーザーの名前
    surname: ユーザーの姓
    usrgrps: 所属するユーザーグループのリスト
    passwd: ユーザーアカウントのログインパスワード
    lang: 言語
    theme: UIのテーマ
    autologin: 自動ログイン(yes|no)
    autologout: ログアウトのタイムアウト値
    refresh: ページの更新間隔
    rows_per_page: ページあたりの表示行数
    after_login_url: ログイン後のURL
    user_medias: ユーザーの通知メディア設定
    user_role: ユーザーのロール
```

`usrgrps`は所属するユーザーグループ名を列挙したリストを定義します。

`autologout`は`30m`等の値を指定します。また、`0`と指定した場合は自動ログアウトが無効になります。

`user_medias`は以下のパラメーターを定義した辞書型のリストを定義します。
```yml
- mediatype: メディアタイプ名
  sendto: 送信先メールアドレス
  period: 通知を実行する期間(1-7,00:00-24:00)
  severity:
    not_classfied: 不明レベルを通知する(yes|no)
    information: 情報レベルを通知する(yes|no)
    warning: 警告レベルを通知する(yes|no)
    average: 軽度の障害レベルを通知する(yes|no)
    high: 重度の障害レベルを通知する(yes|no)
    disaster: 致命的な障害レベルを通知する(yes|no)
  active: 通知設定を有効化(yes|no)
```

`user_role`はユーザーのロールを指定します。デフォルト設定では、以下のいずれかを選択できます。
- Admin role: Zabbix管理者
- Super admin role: Zabbix特権管理者
- Guest role: ゲストユーザー
- User role: 一般ユーザー

以下はZabbixの管理者ユーザーを1つ作成する定義例です。
```yml
zabbix_webapi_users: 
  - state: present
    username: s.abe 
    name: Shinzo
    surname: Abe
    usrgrps:
      - Admins
      - Prime Ministers
    passwd: shinzo00
    lang: en_US
    theme: blue-theme
    autologin: no
    autologout: '0'
    refresh: '30'
    rows_per_page: '200'
    after_login_url: ''
    user_medias:
      - mediatype: Email
        sendto: s.abe@example.go.jp
        period: 1-7,00:00-24:00
        severity:
          not_classified: no
          information: yes
          warning: yes
          average: yes
          high: yes
          disaster: yes  
        active: no 
    user_role: Admin role 
```

# トリガーアクション設定
Zabbixのトリガーアクション設定を定義します。トリガーアクション設定はパラメーターを定義した辞書型のリストを定義します。
```yml
zabbix_webapi_trigger_actions:
  - status: メディアタイプのステータス(enabled:有効|disabled:無効)
    state: 作成/削除(present|absent)
    name: アクション名
    esc_period: デフォルトのアクション実行ステップの間隔
    conditions: 実行条件のリスト
    formula: 計算のタイプ
    operations: 実行内容のリスト
    recovery_operations: 復旧時の実行内容のリスト
```

`conditions`は以下のパラメーターを定義した辞書型のリストで定義します。
```yml
- type: 実行条件のタイプ
  operator: オペレータ 
  value: 実行条件の条件値

  # 条件値が2つ存在する場合
  value2: 実行条件の条件値2

  # 実行条件の数が2件以上存在する場合
  formulaid: 実行条件のID(A,B,C...)
```
実行条件のタイプ及びオペレータ、条件値の設定は、[Ansibleのドキュメント](https://docs.ansible.com/ansible/latest/collections/community/zabbix/zabbix_action_module.html)の`conditions`を確認してください。

`formula`は実行条件の計算式を指定します。
```yml
# 実行条件が1つしかない場合、以下の通り`A`を固定値で指定します。
formula: A

# 実行条件が複数ある場合、`conditions`で指定した`formulaid`を参照する論理計算式を指定します。
formula: A and (B or C)
```

`operations`はのパラメーターを定義した辞書型のリストを定義します。
```yml
- type: send_message
  media_type: 送信するメディアタイプ
  subject: 送信するメッセージの件名
  op_message: 送信するメッセージ 
  send_to_users: 送信するユーザーのリスト
```
これは一般的なメッセージ通知を行う場合の設定値です。他の実行内容を設定する場合は[Ansibleのドキュメント](https://docs.ansible.com/ansible/latest/collections/community/zabbix/zabbix_action_module.html)の`operations`を確認してください。

`op_message`パラメーターは、パイプ`|-`書式を使用し改行を含むテキストを設定できます。
```yml
op_message: |-
  発生時刻：{EVENT.DATE} {EVENT.TIME}
  発生ホスト:{HOST.NAME}({HOST.HOST})
  障害名：{EVENT.NAME}
```

`recovery_operation`は`operation`と同一のパラメーターを定義します。また、`recovery_operation`限定で、`notify_all_involved`タイプにより`send_to_users`オプションを省略できます。
```yml
- type: notify_all_involved 
# イベント通知を行った宛先に復旧通知を送信する。
```

以下はメール通知を行うアクションを設定する定義例です。
```yml
zabbix_webapi_trigger_actions:
  - state: present
    status: enabled
    name: Send alerts to Admin
    esc_period: 1h
    conditions:
      - type: 'trigger_name'
        operator: 'like'
        value: 'Zabbix agent is unreachable'
        formulaid: A
      - type: 'trigger_severity'
        operator: '>='
        value: 'disaster'
        formulaid: B
    formula: A or B
    operations:
      - type: send_message
        media_type: 'Email'
        subject: "Zabbix Alert: {EVENT.NAME}"
        op_message: |-
          Datetime: {EVENT.DATE} {EVENT.TIME}
          Host: {HOST.NAME}({HOST.HOST})
        send_to_users:
          - 'Admin'
    recovery_operations:
      - type: notify_all_involved
        subject: "Zabbix Recovery: {EVENT.NAME}"
        op_message: |-
          Event was closed.
```