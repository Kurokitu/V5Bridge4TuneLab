# G2PAScript Format Writing Instructions

## 1. Syntax

The syntax of G2PAScript is based on Lua, using Lua code to achieve programmable custom G2PA functionality. The syntax interpreter version is LuaJIT (Lua 5.1).

## 2. Installation

The installation location of G2PAScript varies based on usage, with two types: private deployment and public deployment.

* The file extension for G2PAScript is ".lua".
* Public Deployment
  * The deployment location is in the %USERPROFILE%\\.TuneLab\\VOCALOID5\\G2PAScripts directory.
  * It is applicable to all voice banks and can be used by all voice banks by default.
  * When the file name has a language suffix (_JPN, _ENG, _ESP, _KOR, _CHN), the G2PAScript is limited to the specified language's voice bank.
  * The file name of G2PAScript is the display name, but the language suffix will be removed.
* Private Deployment
  * The deployment location is in the voice bank's installation directory, in the same folder as the *.ddi and *.ddb files.
  * It is applicable to a specific voice bank, dedicated to a particular voice bank.
  * A private G2PAScript usually has only one, serving as the default language parser for the voice bank.
  * When there are multiple private G2PAScripts, the first G2PAScript in alphabetical order is the default. Others will be listed with their file names as display names for selection.

## 3. Lyrics Parsing Order

Each VOCALOID voice bank has its own default language parser (G2PA), and G2PAScript's lyrics phoneme matching takes precedence over this language parser.

Typically, the TuneLab host will first attempt to use G2PAScript to parse the phoneme symbols. If parsing fails, it will call the language parser to continue parsing.

## 4. G2PAScript File Format

### 4.1 Script Identifier

To quickly determine if a lua file is a G2PAScript, the beginning of the G2PAScript file should have a script identifier string at the top. The identifier string format is:

```
--G2PAScript
```

There should be no spaces at the beginning, end, or middle of the string, and it is case-insensitive.

* A G2PAScript file with the script identifier should also have an entry function, otherwise, it will not run.

### 4.2 Entry Function

G2PAScript has only one entry function, the function prototype is:

```
string do_g2pa(string lyric)
```

* For the input variable, the content is the lyrics entered for the note, the type is a string, with no extra spaces or line breaks at the beginning and end.
* For the output variable, the content is the phoneme symbols separated by spaces, the type is a string, with no extra spaces or line breaks at the beginning and end.

### 4.3 Functional Function

G2PAScript additionally provides a functional function to call the system's built-in language parser, the function prototype is:

```
string get_lang_phoneme(int langid, string lyric)
```

* Input variable langid, the type is integer, the value range is 0-4, with specific values defined as follows:
  * 0. Japanese
  * 1. English
  * 2. Spanish
  * 3. Korean
  * 4. Mandarin Chinese
* For the input variable lyric, the content is the lyrics entered for the note, the type is a string, with no extra spaces or line breaks at the beginning and end.
* For the output variable, the content is the phoneme symbols separated by spaces, the type is a string, with no extra spaces or line breaks at the beginning and end.

This function can call the system parser to parse the default phoneme symbols, commonly used for handling cross-language phoneme symbols.

### 4.4 System Functions

For security reasons, the program restricts some system functions. The main restrictions are as follows:

* The os library is forbidden to use.
* The open function in the io library is only allowed to open files in read mode.

## 5. Example Code

```
--G2PAScript

-- Define dictionary
phns={}
phns["chang"]="ts_h AN"
phns["chong"]="ts_h UN"

-- Entry function
function do_g2pa(lyric)
    -- Check if it's in the dictionary, if so, return the phoneme
    if phns[lyric] then
        return phns[lyric]
    end
    -- If not in the dictionary, query Japanese phonemes and return (0 represents Japanese)
    return get_lang_phoneme(0, lyric)
end
```

## 6. Debugging

* G2PAScript uses a Lua debugger for debugging. The debugger download address is:

[https://github.com/rjpcomputing/luaforwindows/blob/master/files/lua5.1.exe](https://github.com/rjpcomputing/luaforwindows/blob/master/files/lua5.1.exe)

---

This completes the translation of the provided document.