# SetupLDAP
Setup environment LDAP on Ubuntu Server 22

# 1. Cài đặt OpenLDAP Server
```
sudo apt update
sudo apt install slapd ldap-utils
```

# 2. Cấu hình OpenLDAP Server
```
sudo dpkg-reconfigure slapd
```

Các bước cấu hình bao gồm
- Không bỏ cơ sở dữ liệu hiện có (No)
- Chọn DNS domain name: ví dụ: **localhost**
- Chọn tên tổ chức (Organization name), ví dụ: **Example Organization**
- Xác định mật khẩu quản trị LDAP
- Chọn mức độ xóa cơ sở dữ liệu (Yes)
- Không di chuyển cơ sở dữ liệu ra ngoài (No)

# 3. Thêm sơ đồ LDAP cho người dùng và nhóm
LDAP cần một hồ sơ để xác định các thuộc tính của người dùng và nhóm
```
sudo ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/cosine.ldif
sudo ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/nis.ldif
sudo ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/inetorgperson.ldif
```

# 4. Cấu hình cơ sở dữ liệu LDAP
Tạo một tệp LDIF chưa thông tin cơ sở dữ liệu của bạn. Tạo một tệp ví dụ **base.ldif** với nội dung sau
```
dn: ou=people,dc=localhost,dc=vncsg
objectClass: organizationalUnit
ou: people

dn: ou=groups,dc=localhost,dc=vncsg
objectClass: organizationalUnit
ou: groups
```

Áp dụng cấu hình:
```
ldapadd -x -D cn=admin,dc=localhost -W -f base.ldif
```
Bỏ dc=vncsg :)))))

# 5. Thêm người dùng và nhốm vào LDAP
Bạn có thể thêm ngườ dùng mới với tệp LDIF

Tạo tệp **newuser.ldif**
```
dn: uid=jdoe,ou=people,dc=localhost
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: jdoe
sn: Doe
givenName: John
cn: John Doe
displayName: John Doe
uidNumber: 10000
gidNumber: 10000
userPassword: {SSHA}password_hash
gecos: John Doe
loginShell: /bin/bash
homeDirectory: /home/jdoe
```

Thêm người dùng vào LDAP:
```
ldapadd -x -D cn=admin,dc=localhost -W -f newuser.ldif
```
