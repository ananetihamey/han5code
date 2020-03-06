# 汉五码使用说明

## 什么是汉五码？
可以在任何中文平台自由发布信息的加密编码。

把任何一段文本（广义文本）转化为加密过的汉字编码（密文），用于传播并规避审查。
使用正确的密钥即可解码，看到原始文本内容。

没错，微博，贴吧，QQ，微信，空间，博客，论坛，任何能发汉字的地方都可以。
针对中文网络封锁设计，应用汉五码的范围内，所有现存的面向文本的自动审查，爬网，舆情，过滤类技术和工具都将失效。
公开密钥的情况下，无法完全规避人工审查，但可以使审查的成本指数级增加。
密钥未公开的情况下，有足够的强度保障通信安全，完全规避审查。

## 如何安装？
暂时只发布Windows系统下的安装方法。Win7/8/8.1/10

1. 打开PowerShell，执行命令
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
该命令允许本地执行PS脚本
2. 新建文本文件，并重命名为han5encoder.ps1。这是编码器。
3. 在刚才新建的文件上右键，编辑，在打开的PowerShell ISE窗口中粘贴以下内容，保存。
```
$myKey="白日依山尽黄河入海流欲穷千里目更上一层楼"
$bsKey=[System.Text.Encoding]::Default.GetBytes($myKey)
if($bsKey.length -lt 32){echo "密钥无效";exit}
$mR=New-object System.Security.Cryptography.RijndaelManaged;$mR.Key=$bsKey[0..15]
$msE=New-object System.IO.MemoryStream;$enc=$mR.CreateEncryptor($mR.Key, $mR.IV)
$csE=New-object -TypeName System.Security.Cryptography.CryptoStream -ArgumentList $msE,$enc,1
$swE=New-object System.IO.StreamWriter($csE);$szCp=Get-Clipboard -Format Text -Raw
if($szCp.length -eq 0){echo "Clipboard is empty.";exit}
$swE.Write($szCp);$swE.dispose();$bsCode=@();$bsCode+=$mR.IV;$bsCode+=$mse.ToArray()
$mapHan5="，的一是了我不人在他有这个上们来到时大地为子中你说生国年着就那和要她出也得里后以会家可下而过天去能对小多然于心学么之都好看起发当没成只如事把还用第样道想作种开美总从无情己面最女但现前些所同日手又行意动方期它头经长儿回位分爱老因很给名法间斯知世什两次使身者被高已亲其进此话常与活正感见明问力理尔点文几定本公特做外孩相西果走将月十实向声车全信重三机工物气每并别真打太新比才便夫再书部水像眼等体却加电主界门利海受听表德少克代员许先口由死安写性马光白或住难望教命花结乐色更拉东神记处让母父应直字场平报友关放至张认接告入笑"
foreach($l in $bsCode){$res+=$mapHan5[$l]}Set-Clipboard -Value $res
#Har-em-akhet200305
```

4. 新建文本文件，重命名为han5decoder.ps1。这是解码器。
5. 在刚才新建的文件上右键，编辑，在打开的PowerShell ISE窗口中粘贴以下内容，保存。
```
$myKey="白日依山尽黄河入海流欲穷千里目更上一层楼"
$bsKey=[System.Text.Encoding]::Default.GetBytes($myKey);if($bsKey.length -lt 32){echo "密钥无效";exit}
$mR=New-object System.Security.Cryptography.RijndaelManaged;$mR.Key=$bsKey[0..15];
$szHan5="，的一是了我不人在他有这个上们来到时大地为子中你说生国年着就那和要她出也得里后以会家可下而过天去能对小多然于心学么之都好看起发当没成只如事把还用第样道想作种开美总从无情己面最女但现前些所同日手又行意动方期它头经长儿回位分爱老因很给名法间斯知世什两次使身者被高已亲其进此话常与活正感见明问力理尔点文几定本公特做外孩相西果走将月十实向声车全信重三机工物气每并别真打太新比才便夫再书部水像眼等体却加电主界门利海受听表德少克代员许先口由死安写性马光白或住难望教命花结乐色更拉东神记处让母父应直字场平报友关放至张认接告入笑"
$mapHan5=@{};for($i=0;$i -lt $szHan5.Length;$i++){$mapHan5.Add($szHan5[$i], $i)};$szCp=Get-Clipboard -Format Text -Raw
try{$bsCode=@();for($i=0;$i -lt $szCp.Length;$i++){if(([string]$mapHan5[$szCp[$i]]).Trim()){$bsCode+=$mapHan5[$szCp[$i]]}}
$mR.IV=$bsCode[0..15];$msD=New-object -TypeName System.IO.MemoryStream -ArgumentList @(,$bsCode[16..($bsCode.length-1)])
$enc=$mR.CreateDecryptor($mR.Key, $mR.IV);$csD=New-object -TypeName System.Security.Cryptography.CryptoStream -ArgumentList $msD,$enc,0
$srD=New-object System.IO.Streamreader($csD);$out=$srD.ReadToEnd();Set-Clipboard -Value $out;[System.Windows.Forms.MessageBox]::Show($out,"",0)}
catch{echo "未完成解析";exit}
#Har-em-akhet200305
```

## 如何使用
* 关于密钥：可以是任意字符，长度应不小于16个汉字（32字节）
* 关于密钥发布的方式：
    公开发布的信息：可以置于个人资料，密文回帖等。或通过其它渠道发布（用户电报群等）
* 关于密钥内容：以一个聪明的，无法自动化处理的方式，告知接收方。
    例如：杜甫《登高》后三句无标点
    例如：《哈利波特》邓布利多的最后一句话

### 编码
1. 将$myKey="..."，既第一句中的诗词，替换为你自己的密钥，长度应大于16个汉字
2. 将需要编码的文本复制到剪贴板(Ctrl+C)
3. 右击han5encoder.ps1，“使用PowerShell运行”
4. 此时，经过加密编码的文字已替换剪贴板中的文字，到目标位置粘贴发布即可

### 解码
1. 将$myKey="...",既第一句中的诗词，替换为密文对应的密钥，密钥不正确无法解码密文
2. 将密文复制到剪贴板(Ctrl+C)
3. 右击han5decoder.ps1，“使用PowerShell运行"
4. 如果解码成功，将会以消息框的方式弹出文本内容，并自动复制到剪贴板
* 如果密钥没有变化，可以跳过step1.
