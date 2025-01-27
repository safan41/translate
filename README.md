# `translatepy` (originally: translate)

### An aggregation of multiple translation API

***Translate, transliterate, get the language of texts in no time with the help of multiple APIs!***

[![PyPI version](https://badge.fury.io/py/translatepy.svg)](https://pypi.org/project/translatepy/)
[![PyPI - Downloads](https://img.shields.io/pypi/dm/translatepy)](https://pypistats.org/packages/translatepy)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/translatepy)](https://pypi.org/project/translatepy/)
[![PyPI - Status](https://img.shields.io/pypi/status/translatepy)](https://pypi.org/project/translatepy/)
[![GitHub - License](https://img.shields.io/github/license/Animenosekai/translate)](https://github.com/Animenosekai/translate/blob/master/LICENSE)
[![GitHub top language](https://img.shields.io/github/languages/top/Animenosekai/translate)](https://github.com/Animenosekai/translate)
[![CodeQL Checks Badge](https://github.com/Animenosekai/translate/workflows/CodeQL%20Python%20Analysis/badge.svg)](https://github.com/Animenosekai/translate/actions?query=workflow%3ACodeQL)
[![Pytest](https://github.com/Animenosekai/translate/actions/workflows/pytest.yml/badge.svg)](https://github.com/Animenosekai/translate/actions/workflows/pytest.yml)
![Code Size](https://img.shields.io/github/languages/code-size/Animenosekai/translate)
![Repo Size](https://img.shields.io/github/repo-size/Animenosekai/translate)
![Issues](https://img.shields.io/github/issues/Animenosekai/translate)

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

You will need Python 3 to use this module

```bash
# vermin output
Minimum required versions: 3.2
Incompatible versions:     2
```

According to Vermin (`--backport typing`), Python 3.2 is needed for the backport of typing but some may say that it is available for python versions higher than 3.0

Always check if your Python version works with `translatepy` before using it in production

## Installing

### Option 1: From PyPI

```bash
pip install --upgrade translatepy
```

### Option 2: From Git

```bash
pip install --upgrade git+https://github.com/Animenosekai/translate
```

You can check if you successfully installed it by printing out its version:

```bash
$ translatepy --version
# output:
translatepy v2.0
```

or just:

```bash
$ python -c "import translatepy; print(translatepy.__version__)"
# output:
translatepy v2.0
```

## List of Built-in Services

- [Microsoft Bing Translator](https://www.bing.com/translator)
- [DeepL](https://www.deepl.com/translator)
- [Google Translate](https://translate.google.com)
- [LibreTranslate](https://libretranslate.com)
- [MyMemory](https://mymemory.translated.net)
- [Reverso](https://www.reverso.net/text_translation.aspx)
- [Translate.com](https://www.translate.com)
- [Yandex Translate](https://translate.yandex.com)

All of the names belong to their respective rightholders.

## Usage

### Command line interface mode

#### Interactive Shell (REPL)

```bash
$ translatepy shell
## Choose the action
[?] What do you want to do?: Translate
 > Translate
   Transliterate
   Spellcheck
   Language
   Example
   Quit

## Choose the language to translate in (this step can be skipped by passing the `--dest-lang` argument when starting the program)
In what language do you want to translate in?
[?] (translatepy ~ Select Lang.) > : ...

## Translate
Enter '.quit' to stop translating
(translatepy ~ Translate) > ... # type in whatever you want to translate
```

#### In other applications/from the terminal

Select an action:
{translate,transliterate,language,spellcheck}

and pass it as a command with the right arguments:

```bash
$ translatepy translate --dest-lang Français --text Hello
{
    "success": true,
    "service": "Google",
    "source": "Hello",
    "sourceLanguage": "eng",
    "destinationLanguage": "fra",
    "result": "Bonjour"
}
```

### In Python script

#### The Translator Class

The translator lets you group and use multiple translators at the same time, to increase your chance on getting an answer.

It takes two *optional* arguments: the `services_list` argument, which is a list of `Translator` objects and the second one being the `request` argument which is the object which will be used to make requests.

It has all of the supported methods.

- translate: To translate things
- transliterate: To transliterate things
- spellcheck: To check the spelling of a text
- language: To get the language of a text
- example: To get a list of examples of a word
- dictionary: To get a list of translations categorized into "featured" and "less common" by DeepL and Linguee
- text_to_speech: To get an MP3 file containing the speech version of the given text

When something goes wrong or nothing got found, an exception will be raised.

```python
>>> from translatepy import Translator
>>> translator = Translator()
>>> translator.translate("Hello", "French")
TranslationResult(service=Yandex, source=Hello, source_language=auto, destination_language=French, result=Bonjour)
>>> translator.language("こんにちは")
LanguageResult(service=Yandex, source=こんにちは, result=Language(jpn))
```

#### Translators

You can use each translators separately by using them the same way as you would with `translatepy.Translator`

```python
>>> from translatepy.translators.google import GoogleTranslate
>>> gtranslate = GoogleTranslate()
>>> gtranslate.translate("Hello World", "Japanese")
TranslationResult(service=Google, source=Hello World, source_language=eng, destination_language=jpn, result=こんにちは世界)
```

And some translators have their own parameters which can't be configured by the `Translator` class for now:

```python
>>> gtranslate_china = GoogleTranslate(service_url="translate.google.cn")
>>> gtranslate_china.translate("Hello World", "Japanese")
TranslationResult(service=Google, source=Hello World, source_language=eng, destination_language=jpn, result=こんにちは世界)
```

#### The Language Class

The language class contains lots of information about a language.

You need to pass the language name or code to the class initialization:

```python
>>> from translatepy import Language
>>> Language("French")
# Returns a Language class with the "fra" language
>>> Language("en")
# Returns a Language class with the "eng" language
>>> Language("eng")
# Returns a Language class with the "eng" language
>>> Language("日本語")
# Returns a Language class with the "jpn" language
```

The Language Class contains both the ISO 639-1 Alpha-2 language code and the ISO 639-2 Alpha-3 language code.

Each available language has its own ID, coming from the Alpha-3 Language Code most of the times (but which is also unique for languages such as the "Automatic" Language and the "Emoji" one)

It also contains the language name for a lot of languages:

```python
>>> Language("Français").in_foreign_languages.get("ja", None) # an alpha-2 code needs to be passed in, also make sure to have a fallback such as None here because not all of the languages had been translated.
'フランス語'
```

It also contains the "similarity" attribute which gives back a number between 0 and 100 which shows the similarity of the input language with what it found in the language code database:

```python
>>> round(Language("French").similarity, 2)
100.0
>>> Language("Englesh").similarity
94.86832980505137
```

A `translatepy.exceptions.UnknownLanguage` exception is raised if the given language is unknown.

This exception contains the most similar language along with its similarity:

```python
try:
    language = Language("中国")
except translatepy.exceptions.UnknownLanguage as error:
    print("The similarity seemed to be too low for translatepy to accept it as a correct language name")
    print("The language found is:", error.guessed_language)
    print("Its similarity from the passed input is:", str(error.similarity))
```

### Results

All of the methods should have its own result class (defined in [translatepy/models.py](translatepy/models.py)) which all have at least the service, source, result attributes and a "as_json" method to convert everything into a JSON String.

### Plugins

You can make your own `Translator` using the `translatepy.translators.base.BaseTranslator` class.

Make sure that you inherit from this class when creating your translator and to **follow the instruction from [plugin.md](plugin.md)**

## Caching

All of the operations are cached to provide the best performances

You can empty the cache by calling the method "`clean_cache`"

## Deployment

This module is currently in development and might contain bugs.

Feel free to use it in production if you feel like it is suitable for your production even if you may encounter issues.

## Built With

- [pyuseragents](https://github.com/Animenosekai/useragents) - To generate the "User-Agent" HTTP header
- [requests](https://github.com/psf/requests) - To make HTTP requests
- [beautifulsoup4](https://pypi.org/project/beautifulsoup4/) - To parse HTML

## Authors

- **Anime no Sekai** - *Initial work* - [Animenosekai](https://github.com/Animenosekai)
- **Zhymabek Roman** - *Major Contributor (PR #10, PR #15)* - [ZhymabekRoman](https://github.com/ZhymabekRoman)

## Disclaimer

Please **do not** use this module in a commercial manner. Pay a proper API Key from one of the services to do so.

## License

This project is licensed under the GNU Affero General Public License v3.0 License - see the [LICENSE](LICENSE) file for details

### Dataset

The 'playground' folder contains a lot of our search and results for the language management on `translatepy`

Please ask us if you want to use them in another project.

## Acknowledgments

- Thanks to @ZhymabekRoman (Zhymabek Roman) for working on making Yandex more stable and on the v2!
- Inspired by py-googletrans (by @ssut) (especially the thread: [Issue #268](https://github.com/ssut/py-googletrans/issues/268))
