# rocketchat
Tweaks [RocketChat] on ldap filter allows better intergration with [Zimbra Collaboration] using [Zimbra-Rocket]

Package version
- [RocketChat] 2.4.0 from https://releases.rocket.chat/2.4.0/download
- [Zimbra-Rocket] 0.0.3b
- [Zimbra Collaboration] 8.8.15
- [LDAP] Group is using **groupOfNames** not *posixGroup*

[RocketChat]: https://rocket.chat/
[Zimbra-Rocket]: https://github.com/Zimbra-Community/zimbra-rocket
[Zimbra Collaboration]: https://wiki.zimbra.com/wiki/Zimbra_Releases/8.8.15

Changes are made to /opt/Rocket.Chat/programs/server/app/app.js only. 

[Zimbra Collaboration] will use email as uid but when it import into [RocketChat], it will change from __*user\[@]domain.com*__ to __*user\[.]domain.com*__, and [RocketChat] heavily relies on its user.username variable to identify [LDAP] sync, in order to solve this issue, I have switch keyword ``#{username}`` in RocketChat to ``#{name}`` which in terms will use ``${user.email}`` for [LDAP] ``groupOfNames`` lookup purpose. However, due to strange [RocketChat] login procedure, it will sync user's [LDAP] info at login again, however, and it can only use ``#{username}`` or ``#{userdn}`` and at login stage, due to ``user.email`` can be sync from [LDAP] so at this stage, it is still not defined, however, for [Zimbra-Rocket] integration email address shouldn't be changed due to it will be a different [RocketChat] account if email address is changed.

How to install:<br>  
```
    wget https://raw.githubusercontent.com/elurex/rocketchat/master/app.js;
    chowwn rocketchat:rocketchat app.js; 
    mv app.js /opt/Rocket.Chat/programs/server/app/app.js; 
```
[RocketChat] Configuration

Administration -> Accounts -> Iframe
- Enabled: yes
- Iframe URL: *\[zimbra url]*/service/extension/rocket?action=redirect
- API URL: *\[zimbra url]*/service/extension/rocket?action=signOn
- Api Method: Get 
**please read [Read Me]** You must make sure once you login into your *\[zimbra url]* and go to your *\[API URL]* it generates token before you enable above setting, or you may lock out yourself from rocketchat before you enable above setting. (or you need to doing via rocket rest api is able iframes)

Administration -> LDAP -> Sync/Import
- Username Field: mail
- Unique Identifier Field: uid,entryUUID
- Default Domain: *example.com*  __*please change to your domain*__
- Merge Existing Users: yes
- Sync User Data: yes
- User Data Field Map: {"gecos": "name","mail": "email"}
- Sync LDAP Groups: yes
- Auto Remove User Roles: yes
- User Group Filter: (&(cn=#{groupName})(member=cn=#{name},ou=users,*dc=example,dc=com*)) __*please change to your domain*__
- LDAP Group BaseDN: ou=groups,*dc=example,dc=com* __*please change to your domain*__
- User Data Group Map: { "sudoers": "admin" } __*add or modify your mapping to role in rocketchat*__
- Auto Sync LDAP Groups to Channels: yes
- Channel Admin: rocket.chat
- LDAP Group Channel Map: { "sudoers": \[ "itadmin", "general" ] } __*add or modify your mapping to channel in rocketchat*__
- Auto Remove Users from Channels : no
- Sync User Avatar: yes __*use jpegPhoto*__
- Background Sync: yes
- Background Sync Interval: 24 hours
- Background Sync Import New Users
- Background Sync Update Existing Users

Administration -> LDAP -> User Search
- Filter: (&(objectClass=person)(memberOf=cn=rocketchat,ou=groups,*dc=exmaple,dc=com)*) __*please change to your domain*__

[Read Me]: https://github.com/Zimbra-Community/zimbra-rocket/blob/master/README.md
[LDAP]: https://github.com/fuyuanli/vinas-ldap-intergration?fbclid=IwAR1EMRsTuvuOv2Ht69QHiCkhZO-sDo8_ynKB4BuKyDYAjV05oq6NUN0GZr8


