# 怎么保存密码

在很多账户中管理不同的密码通常是困难的: 复杂的密码，包括手工制作的和由随机密码生成器生成的密码，很难记住，但是简单的密码通常不安全。 像 LastPass 这样的云密码管理器也不是一个安全的选择: 云密码管理器经常会遇到各种安全问题。 除此之外，把你的密码暴露给云密码管理公司也不是什么好事。 

1. 使用固定密码+随机数，随机数借助于用户名或者id，使用单向Hash函数生成

在这里，管理密码的基本策略是让每个帐户的密码都遵循“前缀 + 校验和”方案。 所有账户的前缀都是相同的，与通常的密码类似，例如，它可以是你当前的一个密码ーー这是你需要记住的密码。 校验和部分对于每个帐户都是唯一的: 它可以是与主机相关的东西的 md5 / sha-1 / SHA-2校验和的一部分，例如网站的域名，或主机帐户的公司名称等。 这样，只需要记住一个全局前缀就可以轻松管理，而且每个账户的校验和部分都是唯一的，这带来了安全性。 例如，如下图所示，对于您在 quitter.se 的帐户，您可以使用字符串“ quitter.se”的 MD5校验和，它是00b34f415b15dbea2e9d0611d2cc90f8。 然后，使用如 my-password 这样的前缀，遵循“ prefix + checksum”方案，quitter.se 的密码将是 my-password00b34f415b15dbea2e9d0611d2cc90f8。 如果只使用校验和的一部分，例如校验和的前10个字符，那么密码就是 my-password00b34f415b。
![image](images/prefix-checksum.svg)

2. 借助于诗词等便于记忆的东西，进行规则转换。
比较经典的例子如
- 通过经典的斐波那契数列转换 电影**达芬奇密码**中曾使用，当然可以扩展到其他的数列中
- 经典的笑话，饭店的wifi密码: lyb82ndlf (来一杯82年的拉菲）

类似的可参考[黄老邪使用古诗词创建好记的密码 #22](https://github.com/bingoohuang/blog/issues/22)

## 参考
[Manage Passwords for Multiple Accounts with Checksums](https://www.topbug.net/blog/2016/04/30/manage-passwords-for-multiple-accounts-with-checksums/)
