<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Libraries](#libraries)
  - [System Programming in Haskell](#system-programming-in-haskell)
  - [Data.Time](#datatime)
    - [System](#system)
    - [Get current year / month / day in Haskell](#get-current-year--month--day-in-haskell)
    - [Get Current Time](#get-current-time)
    - [Documentation By Examples - GHCI shell](#documentation-by-examples---ghci-shell)
      - [Date Time Manipulation](#date-time-manipulation)
      - [Difference between two dates](#difference-between-two-dates)
      - [Day in a Week/Month/Year or Week Number](#day-in-a-weekmonthyear-or-week-number)
      - [Parsing Dates and Times from Strings](#parsing-dates-and-times-from-strings)
      - [Printing a Date](#printing-a-date)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Libraries

### System Programming in Haskell

**Directory**

Get current Directory
```
> import System.Directory
> 
> getCurrentDirectory 
"/home/tux/PycharmProjects/Haskell"
> 
```

Change Current Directory
```haskell
> import System.Directory
> 
> setCurrentDirectory "/"
> 
> getCurrentDirectory 
"/"
> 
getCurrentDirectory :: IO FilePath
> 
> 
> fmap (=="/") getCurrentDirectory 
True
> 
> liftM (=="/") getCurrentDirectory 
True
> 
```

List Directory Contents
```haskell
> import System.Directory
>
>  getDirectoryContents "/usr"
[".","include","src","local","bin","games","share","sbin","lib",".."]
> 
> :t getDirectoryContents 
getDirectoryContents :: FilePath -> IO [FilePath]
> 
```

Special Directories Location
```haskell
> getHomeDirectory
"/home/tux"
> 
> getAppUserDataDirectory "myApp"
"/home/tux/.myApp"
> 
> getUserDocumentsDirectory
"/home/tux"
> 
```

**Running External Commands**

```haskell
> import System.Cmd
> 
> :t rawSystem
rawSystem :: String -> [String] -> IO GHC.IO.Exception.ExitCode
> 
> rawSystem "ls" ["-l", "/usr"]
total 260
drwxr-xr-x   2 root root 118784 Abr 26 03:38 bin
drwxr-xr-x   2 root root   4096 Abr 10  2014 games
drwxr-xr-x 131 root root  36864 Mar 11 01:38 include
drwxr-xr-x 261 root root  53248 Abr 14 16:46 lib
drwxr-xr-x  10 root root   4096 Dez  2 18:55 local
drwxr-xr-x   2 root root  12288 Abr  3 13:28 sbin
drwxr-xr-x 460 root root  20480 Abr 26 03:38 share
drwxr-xr-x  13 root root   4096 Jan 13 21:03 src
ExitSuccess
>
```

**Reading input from a system command in Haskell**

This command executes ls -la and gets the output.

```haskell
import System.Process
test = readProcess "ls" ["-a"] ""


> import System.Process
> let test = readProcess "ls" ["-a"] ""
> 
> :t test
test :: IO String
> 
> test >>= putStrLn 
.
..
adt2.py
adt.py
build.sh
build_zeromq.sh
clean.sh
codes
comparison.ods
dict.sh
ffi
figure1.png
.git


```

### Data.Time

#### System 

#### Get current year / month / day in Haskell

UTC time:

Note that the UTC time might differ from your local time depending on the timezone.

```haskell
import Data.Time.Clock
import Data.Time.Calendar

main = do
    now <- getCurrentTime
    let (year, month, day) = toGregorian $ utctDay now
    putStrLn $ "Year: " ++ show year
    putStrLn $ "Month: " ++ show month
    putStrLn $ "Day: " ++ show day
```

#### Get Current Time

Local time:

It is also possible to get your current local time using your system’s default timezone:

```haskell
import Data.Time.Clock
import Data.Time.Calendar
import Data.Time.LocalTime

main = do
    now <- getCurrentTime
    timezone <- getCurrentTimeZone
    let zoneNow = utcToLocalTime timezone now
    let (year, month, day) = toGregorian $ localDay zoneNow
    putStrLn $ "Year: " ++ show year
    putStrLn $ "Month: " ++ show month
    putStrLn $ "Day: " ++ show day
```


```haskell
import Data.Time.Clock
import Data.Time.LocalTime

main = do
    now <- getCurrentTime
    timezone <- getCurrentTimeZone
    let (TimeOfDay hour minute second) = localTimeOfDay $ utcToLocalTime timezone now
    putStrLn $ "Hour: " ++ show hour
    putStrLn $ "Minute: " ++ show minute
    -- Note: Second is of type @Pico@: It contains a fractional part.
    -- Use @fromIntegral@ to convert it to a plain integer.
    putStrLn $ "Second: " ++ show second
```

#### Documentation By Examples - GHCI shell

##### Date Time Manipulation

```haskell
import Data.Time

> :t getCurrentTime
getCurrentTime :: IO UTCTime
> 


> t <- getCurrentTime
> t
2015-03-04 23:22:39.046752 UTC
> 

> :t t
t :: UTCTime


> today <- fmap utctDay getCurrentTime 
> today
2015-03-04
> :t today
today :: Day
> 
> 

>  let (year, _, _) = toGregorian today
> year
2015
> 

> :t fromGregorian 2015 0 0
fromGregorian 2015 0 0 :: Day

> fromGregorian 2015 0 0
2015-01-01
> 

> diffDays today (fromGregorian year 0 0)
62
> 

> import Text.Printf
>
> tm <- getCurrentTime
>  let (year, month, day) = toGregorian (utctDay tm)
> year
2015
> month
3
> day
4
> 

> printf "The current date is %04d %02d %02d\n" year month day
The current date is 2015 03 04


> import System.Locale
> 
> fmap (formatTime defaultTimeLocale "%Y-%m-%d") getCurrentTime
"2015-03-04"
> 
> 

```

##### Difference between two dates

```haskell

> import Data.Time
> import Data.Time.Clock.POSIX
> 

> let bree = UTCTime (fromGregorian 1981 6 16) (timeOfDayToTime $ TimeOfDay 4 35 25) -- 1981-06-16 04:35:25 UTC
> bree
1981-06-16 04:35:25 UTC
> 


> let nat  = UTCTime (fromGregorian 1973 1 18) (timeOfDayToTime $ TimeOfDay 3 45 50) -- 1973-01-18 03:45:50 UTC
> nat
1973-01-18 03:45:50 UTC
> 


> 
> let bree' = read "1981-06-16 04:35:25" :: UTCTime
> bree'
1981-06-16 04:35:25 UTC
> :t bree'
bree' :: UTCTime
> 
> let nat'  = read "1973-01-18 03:45:50" :: UTCTime
> 
> nat'
1973-01-18 03:45:50 UTC
> 


> difference = diffUTCTime bree nat / posixDayLength
> difference 
3071.03443287037s
> 

>  "There were " ++ (show $ round difference) ++ " days between Nat and Bree"
"There were 3071 days between Nat and Bree"
> 
```

##### Day in a Week/Month/Year or Week Number

```haskell

> import Data.Time
> import Data.Time.Calendar.MonthDay
> import Data.Time.Calendar.OrdinalDate
> import System.Locale

> :t fromGregorian
fromGregorian :: Integer -> Int -> Int -> Day

> let (year, month, day) = (1981, 6, 16) :: (Integer , Int , Int )
> 
> let date = (fromGregorian year month day)
> date
1981-06-16
> 
> let (week, week_day) = sundayStartWeek date
> week
24
> week_day
2
> 

> let (year_, year_day) = toOrdinalDate date
> year_
1981
> year_day
167
> 


> let (week_day_name, _) = wDays defaultTimeLocale !! week_day
> week_day_name
"Tuesday"
> 

> :t defaultTimeLocale 
defaultTimeLocale :: TimeLocale
> 
> defaultTimeLocale 
TimeLocale {wDays = [("Sunday","Sun"),("Monday","Mon"),("Tuesday","Tue"),("Wednesday","Wed"),("Thursday","Thu"),("Friday","Fri"),("Saturday","Sat")], months = [("January","Jan"),("February","Feb"),("March","Mar"),("April","Apr"),("May","May"),("June","Jun"),("July","Jul"),("August","Aug"),("September","Sep"),("October","Oct"),("November","Nov"),("December","Dec")], intervals = [("year","years"),("month","months"),("day","days"),("hour","hours"),("min","mins"),("sec","secs"),("usec","usecs")], amPm = ("AM","PM"), dateTimeFmt = "%a %b %e %H:%M:%S %Z %Y", dateFmt = "%m/%d/%y", timeFmt = "%H:%M:%S", time12Fmt = "%I:%M:%S %p"}
> 
> 
```

##### Parsing Dates and Times from Strings

```haskell
> import Data.Time
> import Data.Time.Format
> import Data.Time.Clock.POSIX
> import System.Locale

> let day:: Day ; day = readTime defaultTimeLocale "%F" "1998-06-03"
> 
> day
1998-06-03
> 

```

##### Printing a Date

```haskell
> import Data.Time
> import Data.Time.Format
> import System.Locale
> 
> now <- getCurrentTime
> :t now
now :: UTCTime
> 
> formatTime defaultTimeLocale "The date is %A (%a) %d/%m/%Y" now
"The date is Wednesday (Wed) 04/03/2015"
> 
> 

> let t = do now <- getCurrentTime ; return $ formatTime defaultTimeLocale "The date is %A (%a) %d/%m/%Y" now
> t
"The date is Wednesday (Wed) 04/03/2015"
> 


```


Credits: 

* http://techoverflow.net/blog/2014/06/13/get-current-year-month-day-in-haskell/
* http://techoverflow.net/blog/2014/08/24/get-current-hour-minute-second-in-haskell/
* http://pleac.sourceforge.net/pleac_haskell/datesandtimes.html


### Bytestring

Bytestring is a built in library for fast and efficient string and binary data processing. ByteStrings are not designed for Unicode. For Unicode strings the Text type must be used from the text package. 


There are two flavours of bytestring, the lazy and strict.

* Strict Bytestring: Data.ByteString

String as a single large array, suitable for passing data between C and Haskell. 

* Lazy   Bytestring: Data.ByteString.Lazy 

Lazy list of strict chunks which makes it suitable for I/O streaming tasks

To avoid name conflicts the modules must be imported as qualified.

```haskell

import qualified Data.ByteString as B
import qualified Data.ByteString.Char8 as BC

import qualified Data.ByteString.Lazy as BL
import qualified Data.ByteString.Lazy.Char8 as BLC
```

Examples:

```haskell

> import qualified Data.ByteString as B
> import qualified Data.ByteString.Char8 as BC
> 
> import qualified Data.ByteString.Lazy as BL
> import qualified Data.ByteString.Lazy.Char8 as BLC
> 

{- String to Bytestring -}
{---------------------------------------------}

> BC.pack "hello world" 
"hello world"

> BC.pack "čušpajž日本語"
"\ruapaj~\229,\158"
> 
> :t BC.pack "čušpajž日本語"
BC.pack "čušpajž日本語" :: B.ByteString
> 

{- Strict to Lazy Conversion -}
{---------------------------------------------}

> let s_strict = BC.pack  "čušpajž日本語"
> s_strict 
"\ruapaj~\229,\158"
> 

> let s_lazy = BLC.pack  "čušpajž日本語" 
> s_lazy 
"\ruapaj~\229,\158"
> 
> :t s_
s_lazy    s_strict
> :t s_lazy 
s_lazy :: BL.ByteString
> 

{- Strict to Lazy -}
   
> :t BL.fromChunks
BL.fromChunks :: [B.ByteString] -> BL.ByteString
> 

> let new_lazy = BL.fromChunks [s_strict]
> new_lazy 
"\ruapaj~\229,\158"
> :t new_lazy
new_lazy :: BL.ByteString
> 


{- Lazy to Strict -}
{---------------------------------------------}

> let new_strict = B.concat $ BL.toChunks s_lazy 
> new_strict 
"\ruapaj~\229,\158"
> :t new_strict
new_strict :: B.ByteString
> 

> BC.putStrLn new_strict 
uapaj~�,�
> 

> BLC.putStrLn new_lazy 
uapaj~�,�
> 


{- Serialization -}
{---------------------------------------------}

> import qualified Data.Binary as D
> {- Serializes Haskell Data to Lazy Bytestring -}
> 

> :t D.encode
D.encode :: D.Binary a => a -> BL.ByteString
> 

> :t D.decode
D.decode :: D.Binary a => BL.ByteString -> a
>


> let a = D.encode 10 
> a
"\NUL\NUL\NUL\NUL\n"
> :t a
a :: BL.ByteString
> 
> D.decode a
()
> D.decode a :: Int
*** Exception: Data.Binary.Get.runGet at position 0: demandInput: not enough bytes
> D.decode a :: Integer
10
> 



> let b = D.encode (Just 10 :: Maybe Int)
> :t b
b :: BL.ByteString
> b
"\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\n"
> 

> D.decode b :: Maybe Int
Just 10
>

> let c = D.encode (Nothing :: Maybe Int)
> c
"\NUL"
>
> D.decode c :: Maybe Int
Nothing
>


> let d = D.encode (3.11415e4 :: Double)
> d
"\SOH\SOH\NUL\NUL\NUL\NUL\NUL\NUL\NUL\a\NUL\NUL\NUL\NUL`i\RS\255\255\255\255\255\255\255\218"
> 

{- Bytes to Bytestring -}
{---------------------------------------------}

{- Bytes are encoded as Word8 -}

> :t B.pack
B.pack :: [GHC.Word.Word8] -> B.ByteString
> :t B.unpack
B.unpack :: B.ByteString -> [GHC.Word.Word8]
> 
> :t BL.pack
BL.pack :: [GHC.Word.Word8] -> BL.ByteString
> :t BL.unpack
BL.unpack :: BL.ByteString -> [GHC.Word.Word8]
> 

> let msg = B.pack [72,101,108,108,111,32,119,111,114,108,100]
> msg
"Hello world"

> let bytes = B.unpack msg
> bytes
[72,101,108,108,111,32,119,111,114,108,100]
> 

> :t msg
msg :: B.ByteString

> :t bytes
bytes :: [GHC.Word.Word8]
> 

    {- Word8 to Int -}

> :t fromIntegral 
fromIntegral :: (Num b, Integral a) => a -> b
> 

> let x = 100 :: GHC.Word.Word8
> x
100
> :t x
x :: GHC.Word.Word8
> 

> let y = fromIntegral x :: Int
> y
100
> :t y
y :: Int
> 

> fromIntegral ( 100 :: GHC.Word.Word8) :: Int
100
> 

> let bytes_int = map (\x -> fromIntegral x :: Int) bytes
> bytes_int 
[72,101,108,108,111,32,119,111,114,108,100]
> :t bytes_int 
bytes_int :: [Int]
> 

> let bytes_word8 = map (\x -> fromIntegral x ::  GHC.Word.Word8) bytes_int 
> bytes_word8 
[72,101,108,108,111,32,119,111,114,108,100]
> :t bytes_word8 
bytes_word8 :: [GHC.Word.Word8]
> 



```

#### Documentation in Hackage

**ByteString**

*An efficient compact, immutable byte string type (both strict and lazy) suitable for binary or 8-bit character data.*

*The ByteString type represents sequences of bytes or 8-bit characters. It is suitable for high performance use, both in terms of large data quantities, or high speed requirements. The ByteString functions follow the same style as Haskell's ordinary lists, so it is easy to convert code from using String to ByteString.*

* [Bytestring Package](http://hackage.haskell.org/package/bytestring)
* [Data.ByteString](http://hackage.haskell.org/package/bytestring-0.10.6.0/docs/Data-ByteString.html)
* [Data.ByteString.Char8](http://hackage.haskell.org/package/bytestring-0.10.6.0/docs/Data-ByteString-Char8.html)
* [Data.ByteString.Lazy.Char8](http://hackage.haskell.org/package/bytestring-0.10.6.0/docs/Data-ByteString-Lazy-Char8.html)

**Data.Word**

*Unsigned integer types.*

[Data.Word](http://hackage.haskell.org/package/base-4.7.0.1/docs/Data-Word.html)

**Data.Binary package**

*Binary serialisation of Haskell values to and from lazy ByteStrings. The Binary library provides methods for encoding Haskell values as streams of bytes directly in memory. The resulting ByteString can then be written to disk, sent over the network, or futher processed (for example, compressed with gzip).*

* [Data.Binary](http://hackage.haskell.org/package/binary-0.5.0.2)


**Data.Bits package**

*This module defines bitwise operations for signed and unsigned integers. Instances of the class Bits for the Int and Integer types are available from this module, and instances for explicitly sized integral types are available from the Data.Int and Data.Word modules.*

* [Data.Bits](https://downloads.haskell.org/~ghc/latest/docs/html/libraries/base/Data-Bits.html)

### GnuPlot

Installation:

```
$ cabal install gnuplot
$ sudo apt-get install gnuplot-x11 # Ubuntu/ Debian
```

Examples:

```haskell
> import Graphics.Gnuplot.Simple
> plotList [] [(1, 1), (2, 2), (3, 3)]
```

```haskell
> import Graphics.Gnuplot.Simple
> plotFunc [] (linearScale 1000 (-20,20)) (\x -> sin x / x)
```

```haskell
> plotList [Title "A title", XLabel "x label"] [(2,10),(3,15),(4,14),(5,19)]
> plotList [Title "A title", XLabel "x label", YLabel "the y label"] [(2,10),(3,15),(4,14),(5,19)]
```

