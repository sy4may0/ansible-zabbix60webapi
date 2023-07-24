Zabbix60webapi
=========

Zabbix-APIによりZabbixの監視設定を行います。

Requirements
------------

Python実行環境で`zabbix-api`ライブラリが必要です。

Role Variables
--------------

変数のデフォルト値はdefaults/main.ymlを確認してください。必要に応じて、パラメーターをデフォルト値から変更する場合、group_vars等、他の変数定義ファイルを作成し値を上書きしてください。

変数の詳細な説明は[Variables](documents/Variables.md)を確認してください。

Dependencies
------------

None.

Example Playbook
----------------

ローカルから実行するため、`become: no`及び`gather_facts: no`を設定します。
```
- hosts: zabbix
  become: no
  gather_facts: no
  roles:
    - zabbix60webapi
```

License
-------

MIT

Author Information
------------------

This role was created in 2023 by [sy4may0](https://github.com/sy4may0).