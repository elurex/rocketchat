# rocketchat
Tweaks [RocketChat] on ldap filter allows better intergration with [Zimbra Collaboration] using [Zimbra-Rocket]

Package version
- [RocketChat] 2.3.2 from https://github.com/RocketChat/Rocket.Chat/archive/2.3.2.tar.gz
- [Zimbra-Rocket] 0.0.3b
- [Zimbra Collaboration] 8.8.15
- LDAP Group is using **groupOfNames** not *posixGroup*

[RocketChat]: https://rocket.chat/
[Zimbra-Rocket]: https://github.com/Zimbra-Community/zimbra-rocket
[Zimbra Collaboration]: https://wiki.zimbra.com/wiki/Zimbra_Releases/8.8.15

Changes are made to /opt/Rocket.Chat/programs/server/app/app.js only. 

How to install:<br>  
```
    wget https://github.com/elurex/rocketchat/raw/master/app.js; 
    chowwn rocketchat:rocketchat app.js; 
    mv app.js /opt/Rocket.Chat/programs/server/app/app.js; 
```
RocketChat Configuration

Administration -> LDAP -> Sync/Import
- Username Field: mail
- Unique Identifier Field: uid,entryUUID
- Default Domain: *example.com*  **please change to your domain**
- Merge Existing Users: yes
- Sync User Data: yes
- User Data Field Map: {"gecos": "name","mail": "email"}
- Sync LDAP Groups: yes
- Auto Remove User Roles: yes
- User Group Filter: (&(cn=#{groupName})(member=cn=#{name},ou=users,*dc=example,dc=com*)) **please change to your domain**
- LDAP Group BaseDN: ou=groups,*dc=example,dc=com* **please change to your domain**
- User Data Group Map: { "sudoers": "admin" } **add or modify your mapping to role in rocketchat**
- Auto Sync LDAP Groups to Channels: yes
- Channel Admin: rocket.chat
- LDAP Group Channel Map: { "sudoers": \[ "itadmin", "general" ] }
- Auto Remove Users from Channels : no
- Sync User Avatar: yes **use jpegPhoto**
- Background Sync: yes
- Background Sync Interval: 24 hours
- Background Sync Import New Users
- Background Sync Update Existing Users

Administration -> LDAP -> User Search
- Filter: (&(objectClass=person)(memberOf=cn=rocketchat,ou=groups,*dc=exmaple,dc=com)*) **please change to your domain**



