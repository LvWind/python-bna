# 说明
clone 到本地，安装依赖

```shell
cd python-bna

pip3 install -e ./
```

启动已安装的战网安全令，右上角菜单`序列号与还原码`获得`CN`开头的序列号和10位的还原码。

```shell
python3 ./bin/bna restore CN-xxxx-xxxx-xxxx-xxxx ABCDEFGHIJ
```

如成功会显示`Restored CN-xxxx-xxxx-xxxx`

运行

```shell
python3 ./bin/bna show-url
```

输出的`otpauth://totp/Blizzard:CNXXXXXXXXXXXX?secret=XXXXXXXXXXXXXXXXXX&issuer=Blizzard&digits=8` 就是TOPT URL

接着就可以导入第三方OPT软件 1password, Authy等（*注：Google Authenticator不可用，因为战网的是8位数字)

可以找个生成二维码的网站把URL生成二维码方便导入。









# python-bna
[![Build Status](https://api.travis-ci.org/jleclanche/python-bna.svg?branch=master)](https://travis-ci.org/jleclanche/python-bna)

## Requirements

- Python 3.6+


## Command-line usage

bna is a command line interface to the python-bna library. It can store
and manage multiple authenticators, as well as create new ones.


Remember: Using an authenticator on the same device as the one you log in with
is less secure than keeping the devices separate. Use this at your own risk.

Configuration is stored in `~/.config/bna/bna.conf`. You can pass a
different config path with `bna --config=~/.bna.conf` for example.


### Creating a new authenticator

    $ bna new

If you do not already have an authenticator, it will be set as default.
You can pass `--set-default` otherwise.


### Getting an authentication token

    $ bna
    01234567
    $ bna EU-1234-1234-1234
    76543210


### Getting an authenticator's restore code

    $ bna show-restore-code
    Z45Q9CVXRR
    $ bna restore EU-1234-1234-1234 ABCDE98765
    Restored EU-1234-1234-1234


### Getting an OTPAuth URL

To display the OTPAuth URL (used for setup QR Codes):

    $ bna show-otpauth-url
    otpauth://totp/Blizzard:EU123412341234:?secret=ASFAS75ASDF75889G9AD7S69AS7697AS&issuer=Blizzard&digits=8

Now paste this to your OTP app, or convert to QRCode and scan, or
manually enter the secret.

This is compatible with standard TOTP clients and password managers such as:
- [andOTP](https://play.google.com/store/apps/details?id=org.shadowice.flocke.andotp) (Android),
- [KeepassXC](https://keepassxc.org/) (Cross-platform)
- [1Password](https://1password.com/) (Cross-platform)


#### Getting a QR code

To encode to a QRCode on your local system install \'qrencode\'

For a PNG file saved to disk :

    $ bna --otpauth-url | qrencode -o ~/BNA-qrcode.png
    # Scan QRCode
    $ rm ~/BNA-qrcode.png

Or to attempt ot display QRCode in terminal as text output :

    $ bna --otpauth-url | qrencode -t ANSI


## Python library usage

### Requesting a new authenticator

```py
import bna
try:
    # region is EU or US
    # note that EU authenticators are valid in the US, and vice versa
    serial, secret = bna.request_new_serial("US")
except bna.HTTPError as e:
    print("Could not connect:", e)
```

### Getting a token

```py
    # Get and print a token using PyOTP
    from pyotp import TOTP
    totp = TOTP(secret, digits=8)
    print(totp.now())
```
