# Set Vazirmatn font globally for Persian (Farsi) text

I finally could find a proper minimal config to change Farsi font systemwide (to something good, like **[Vazirmant](https://rastikerdar.github.io/vazirmatn/fa)**) and keep other fonts untouched. 

So everywhere on the system (Desktop, browser, ...) Vazirmatn font is used (unless the website/application explicitly set another font that supports Farsi)

<div dir="auto" align="right">

# تنظیم قلمِ وزیر برای زبان فارسی در کل سیستم
بلاخره تونستم یه کانفیگ برای تنظیم فونت فارسی انجام بدم؛ طوری که فقط متن فارسی تغییر بکنه و بقیه فونت‌های سیستم دست‌نخورده باقی بمونه.

با این کانفیگ، هر جای سیستم (میزکار، مرورگر، ...) از قلم آزادِ **وزیرمتن** برای فارسی استفاده می‌شه (مگر این‌که وب‌سایت/نرم‌افزار به طور صریح قلم دیگه‌ای رو تنظیم کرده باشه که فارسی رو پشتیبانی کنه).


## مراحل
اول از همه، آخرین نسخه فونت وزیرمتن و وزیرکد رو به طور کامل نصب کنید!

* https://rastikerdar.github.io/vazirmatn/fa
* https://rastikerdar.github.io/vazir-code-font/

بعد، فایل کانفیگ رو با اسمی که گفته شده داخل مسیر زیر ایجاد کنید:
<div dir="ltr" align="left">

 ```~/.config/fontconfig/conf.d/91-vazirmatn-farsi.conf```

</div>
 (اون شماره که اول اسمش اومده مهمه و اولویت کانفیگ رو مشخص می‌کنه)
 
 سپس داخل فایل، متن زیر رو قرار بدید:
<div dir="ltr" align="left">

```
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
<fontconfig>
  <!-- Only "sans-serif" and "serif" are selected and set, since Vazirmatn don't have "monospace" font-->
  <!-- Fallback fonts preference order -->
  <!-- Source: https://wiki.archlinux.org/title/Font_configuration/Examples -->
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Vazirmatn</family>
      <family>Noto Sans</family>
      <family>DejaVu Sans</family>
    </prefer>
  </alias>

  <alias>
    <family>serif</family>
    <prefer>
      <family>Vazirmatn</family>
      <family>Noto Serif</family>
      <family>DejaVu Serif</family>
    </prefer>
  </alias>

  <alias>
    <family>monospace</family>
    <prefer>
      <family>Vazir Code</family>
      <family>Noto Sans Mono</family>
    </prefer>
  </alias>
  <!-- Default font for the Persian language -->
  <!-- Source: https://wiki.archlinux.org/title/Font_configuration/Examples -->
  <match>
    <test compare="contains" name="lang"><string>fa</string></test>
    <test name="family"><string>sans-serif</string></test>
    <edit mode="prepend" name="family" binding="same"><string>Vazirmatn</string></edit>
    <edit mode="append" name="family" binding="same"><string>Noto Sans</string></edit>
  </match>

  <match>
    <test compare="contains" name="lang"><string>fa</string></test>
    <test name="family"><string>serif</string></test>
    <edit mode="prepend" name="family" binding="same"><string>Vazirmatn</string></edit>
    <edit mode="append" name="family" binding="same"><string>Noto Serif</string></edit>
  </match>

  <match>
    <test compare="contains" name="lang"><string>fa</string></test>
    <test name="family"><string>serif</string></test>
    <edit mode="prepend" name="family" binding="same"><string>Vazir Code</string></edit>
    <edit mode="append" name="family" binding="same"><string>Noto Sans Mono</string></edit>
  </match>

  <!-- Do the same for Arabic -->
    <match>
    <test compare="contains" name="lang"><string>ar</string></test>
    <test name="family"><string>sans-serif</string></test>
    <edit mode="prepend" name="family" binding="same"><string>Vazirmatn</string></edit>
    <edit mode="append" name="family" binding="same"><string>Noto Sans</string></edit>
  </match>

  <match>
    <test compare="contains" name="lang"><string>ar</string></test>
    <test name="family"><string>serif</string></test>
    <edit mode="prepend" name="family" binding="same"><string>Vazirmatn</string></edit>
    <edit mode="append" name="family" binding="same"><string>Noto Serif</string></edit>
  </match>

  <match>
    <test compare="contains" name="lang"><string>fa</string></test>
    <test name="family"><string>serif</string></test>
    <edit mode="prepend" name="family" binding="same"><string>Vazir Code</string></edit>
    <edit mode="append" name="family" binding="same"><string>Noto Sans Mono</string></edit>
  </match>

</fontconfig>

```

</div>

**نکته**: ممکنه قبلاً از داخل تنظیمات میزکار، فونت رو تنظیم کرده باشید. در این صورت ممکنه به‌جای این کانفیگ بالا، اون رو اثر بده (این نکته رو بررسی نکردم. اگر کسی امتحانش کرده ممنون می‌شم بگه). این میزکار فونت‌ها رو داخل پرونده‌های زیر تعیین می‌کنه:
<div dir="ltr" align="left">

```
~/.fonts.conf
~/.config/fontconfig/fonts.conf
```

</div>
پیشنهاد می‌کنم این فایل‌ها رو پاک کنید (البته قبلش ازشون پشتیبان بگیرید چون ممکنه داخلش تنظیمات دیگه هم باشه). احتمالاً میزکار دوباره ایجادشون کنه. اگر هم از ساختارشون سر در میارید می‌تونید بازشون کنید و تگ‌هایی که مربوط به تعیین فونته رو حذف کنید (داخل ویکی‌آرچ، که پیوندش رو انتهای مقاله گذاشتم، می‌تونید دربارهٔ کانفیگ فونت بیش‌تر بخونید).

**در نهایت** بعد از همه کارهای بالا، دستور زیر رو اجرا کنید:
<div dir="ltr" align="left">

```$ fc-cache -fv```

</div>
و یک‌بار سیستم رو خاموش و روشن کنید که برای کل میزکار هم فراخوانی بشه.

**تمام!**

**پ.ن:** با دستور زیر می‌تونید بررسی کنید که قلم‌ها به درستی تنظیم شده. برای مثال خروجی‌های زیر رو ببینید:
<div dir="ltr" align="left">

```
$ fc-match serif:lang=fa
Vazirmatn-Regular.ttf: "Vazirmatn" "Regular"

$ fc-match sans-serif:lang=fa
Vazirmatn-Regular.ttf: "Vazirmatn" "Regular"

$ fc-match serif:lang=en
NotoSerif-Regular.ttf: "Noto Serif" "Regular"

$ fc-match sans-serif:lang=en
NotoSans-Regular.ttf: "Noto Sans" "Regular"
```

</div>

**البته چندتا توضیح کوچیک:** 

۱. من برای زبان‌های غیر فارسی، فونت Noto رو برای [تایپ‌فیس‌های](https://fa.wikipedia.org/wiki/%D8%AA%D8%A7%DB%8C%D9%BE%E2%80%8C%D9%81%DB%8C%D8%B3) Serif و Sans-serif تنظیم کردم. می‌تونید از فونت دیگه‌ای هم استفاده کنید.

۲. اگه توجه کنید، کانفیگ بالا فونت عربی رو هم تنظیم می‌کنه، چون وزیرمتن عربی رو هم پشتیبانی می‌کنه.

۳. از قلم وزیرکد هم برای نمایش متن‌های فارسی monospace استفاده کردم (مثل کدها و ...) تا زیباتر دیده بشن.

۳. [در این صفحه](https://wiki.archlinux.org/title/Font_configuration) و [این صفحه](https://wiki.archlinux.org/title/Font_configuration/Examples#Default_fonts) می‌تونید توضیحات بیشتر و مثال‌های بیشتری پیدا کنید.
</div>
