### 我就是为了测试下

### 背景
> 现在越来越多的业务涉及到了社交电商, 或者是邀请分享当中
> 但是很多朋友生成的邀请码还是随机的, 导致了需要进行多次查询 (`注册查重复`,`注册用邀请码查信息等`)
> 所以特地设计了这个`邀请码`的编码方案
> 可以用用户 `Id` 进行编码, 同时也可以用编码后的字符串反推回 `Id`
##### 在我的想法当中是这样的:<br/>
1. 用户注册时,查询数据库输入手机是否存在 (常规操作，不能少的)<br/>
2. 手机号验证没有问题后, 将用户登录信息, 存储至 `登录表`<br/>
3. 然后使用 `登录表` 新增后的数据表自增 `Id` 生成邀请码<br/>

###### 这样邀请码就不会存在重复<br/>
###### 同时以后也不会用到邀请码作为查询 `where`<br/>
###### 因为邀请码是可以反推回 `Id` 的情况<br/>

##### 生成一个邀请码
```php
/** 只需要引入即可 */
$app = new \NiceYuv\InviteCode();
$app->encode(1);
```

##### 改变生成邀请码位数
1. 默认情况下,是六位
2. 同时去除掉了 `O` `0` `I` `1` 
3. 去除掉肉眼容易分辨错误的字符后, (26 + 10) - 4 = 32位
4. 正常来说:<br/>
    32^6次方 = 10亿次<br/>
    我们可以得到不同的10亿个邀请码<br/>
5. 但是我们会有补位需求,所以默认是 `30` 个字典字符
```php
$app = new \NiceYuv\InviteCode(8);
$app->encode(1);
// OR
$app = new \NiceYuv\InviteCode();
$app->setMax(8);
```

##### 修改邀请码字典
1. 默认情况下是以下这个字典
2. 大家可以把字典打乱, 到时候每个字符代表的意义就会变更
3. `注意`: `千万不要中途修改字典, 不然需要全量更新`
```php
$dictionaries = array(
        '2', '3', '4', '5', '6', '7', '8', '9',
        'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H',
        'J', 'K', 'L', 'M', 'N', 'P', 'Q', 'R',
        'S', 'T', 'U', 'V', 'W', 'X');
$app = new \NiceYuv\InviteCode(8, $dictionaries);
$app->encode(1);
// OR
$app = new \NiceYuv\InviteCode();
$app->setDictionaries($dictionaries);
```

##### 修改邀请码补位符号
1. 补位符号是决定低位数字的分割线
2. 请使用数组传入, 可以是多个
3. `注意`: `千万不能是字典里面存在的字符`
```php
$complement = array('Y', 'Z');
$app = new \NiceYuv\InviteCode(8, arrray(), $complement);
$app->encode(1);
// OR
$app = new \NiceYuv\InviteCode();
$app->setComplement($complement);
```

##### 编码和解码
```php
$app = new \NiceYuv\InviteCode();
/** 编码 */
$app->encode(1);
/** 解码 */
$app->decode($app->encode(1));
