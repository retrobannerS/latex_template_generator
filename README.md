# Генератор LaTeX шаблонов, использующий Pandoc. <!-- omit in toc -->

## Содержание <!-- omit in toc -->

- [Установка](#установка)
  - [Python](#python)
  - [Pandoc](#pandoc)
  - [TeXLive](#texlive)
    - [Unix](#unix)
    - [Mac OS](#mac-os)
    - [Windows](#windows)
    - [Необходимые библиотеки](#необходимые-библиотеки)
    - [Поддержка русского языка](#поддержка-русского-языка)
- [Настройка](#настройка)
- [Использование](#использование)
- [Пример](#пример)
- [Шаблоны](#шаблоны)
- [Добавление собственных преамбул](#добавление-собственных-преамбул)

## Установка

### Python

Установите [Python](https://www.python.org) для вашей операционной системы.

Все требуемые библиотеки для *Python* записаны в [requirements.txt](scripts/requirements.txt). Для установки нужно выполнить в терминале(находясь в папке с проектом) следующую команду:

```bash
pip3 install -r src/requirements.txt
```

### Pandoc

Установите [Pandoc](https://pandoc.org/getting-started.html) в соответствии с вашей операционной системой.

### TeXLive

Установите минимальную версию *TeXLive*, занимающую минимальное количество места на жестком диске:

#### Unix

Пример установки через пакетный менеджер для *Debian*/*Ubuntu*:

```bash
sudo apt install texlive-base
```

Для Arch Linux:

```bash
sudo pacman -S texlive-core
```

#### Mac OS

Через пакетный менеджер [Homebrew](https://brew.sh):

```bash
brew install --cask basictex
```

Или скачать и установить [BasicTeX.pkg](https://tug.org/mactex/morepackages.html).

#### Windows

Скачайте и зайдите в установщик [install-tl-windows.exe](https://tug.org/texlive/windows.html).
В установщике перейдите в Дополнительно(Advanced), выберите схему, занимающую минимальное место: схема только с инфраструктурой.

#### Необходимые библиотеки

Убедитесь, что *tlmgr* доступен в переменных окружения *PATH*, чтобы его можно было запустить из командной строки или *PowerShell*.

Все требуемые библиотеки для *TeXLive* записаны в [tex-requirements.txt](/tex_requirements.txt).

Для установки на *Unix*/*MacOS* необходимо выполнить следующую команду:

```bash
sudo xargs tlmgr install < tex_requirements.txt
```

Для *Windows* необходимо выполнить в *PowerShell*:

```powershell
Get-Content path_to_project/tex_requirements.txt | ForEach-Object { tlmgr install $_ }
```

не забудьте вместо `path_to_project` вставить путь до проекта.

#### Поддержка русского языка

На данный момент корректная поддержка русского языка доступна только для компилятора `XeLaTeX`.

## Настройка

Все настройки генерации шаблона находятся в конфигурационном файле [build-conf.toml](/build-conf.toml).
Для начала будет достаточно поменять название документа и автора в полях *title* и *author*.
Обо всех флагах в настройках можно почитать в [документации pandoc](https://pandoc.org/MANUAL.html) и [документации eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template/tree/master?tab=readme-ov-file#custom-template-variables).

Настройка титульной страницы производится [внутри шаблона eisvogel](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom.tex#L986C1-L1066C1).
Автор, дата и название документа могут вставляться автоматически из [metadata](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/build-conf.toml#L18C1-L24C12).

В файле [build-conf.toml](/build-conf.toml) есть некоторые настройки, которые нужно включать одновременно, то есть среди них есть зависимость:
| Настройка 1                                                                                                                                                 | Настройка 2                                                                                                                         | Примечание                                                                                                        |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------- |
| pandoc argument: ["--citeproc"](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/build-conf.toml#L12) | `bibliography`                                                                                                                      | нужен файл с библиографией, чтобы обрабатывать цитирования                                                        |
| [documentclass](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/build-conf.toml#L31)                 | [book](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/build-conf.toml#L160) | если `documentclass` не `article`, то нужно включить `book` чтобы правильно распознавать заголовки вида `chapter` |

## Использование

1. Сделайте необходимые [настройки](#настройка).
2. *Python* скрипт [generate_template.py](/scripts/generate_template.py) формирует временный файл **metadata.yaml** из настроек в конфигурационном файле [build-conf.toml](/build-conf.toml).
3. Выполняется **bash** команда, создающая файлы **src/template/template.tex**, **src/template/titlepage.tex**, **src/template/toc.tex**, которые вы включаете в свой файл (например [main.tex](src/main.tex)), где вы пишете документ, в качестве преамбулы.

Для соединения в единый создания шаблона утилита [pandoc](https://pandoc.org/index.html), которая применяет настройки из [build-conf.toml](/build-conf.toml) для одного из шаблонов [eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template/tree/master?tab=readme-ov-file#custom-template-variables). На выходе получается единый *.tex* файл, который разделяется на **src/template/template.tex**, **src/template/titlepage.tex**, **src/template/toc.tex**

Запуск *Python* скрипта создает сгенерированный шаблон:

```bash
python3 scripts/generate_template.py
```

Папка **example** и файлы:

- [README.md](/README.md)
- [title-page-01.jpg](/title-page-01.jpg)
- [title-page-02.jpg](/title-page-02.jpg)
- [example1.jpg](/example1.jpg)
- [example2.jpg](/example12.jpg)
- [example3.jpg](/example3.jpg)
- [example4.png](/example4.png)

независимы и могут быть удалены для личного использования этого проекта.

## Пример

В корневой папке вы можете встретить **example** - пример сборки файлов в один PDF.

| [![](/example1.jpg)](/example1.jpg) | [![](/example2.jpg)](/example2.jpg) | [![](/example3.jpg)](/example3.jpg) |
| ----------------------------------- | ----------------------------------- | ----------------------------------- |

## Шаблоны

В проекте приведены два шаблона, основанные на шаблоне [eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template/tree/master?tab=readme-ov-file#custom-template-variables).

[*eisvogel-custom.tex:*](/templates/eisvogel-custom.tex)

- [Можно использовать любой `documentclass` из списка `scrartcl, scrbook, scrreprt`.](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom.tex#L76)
- Адаптирован для русского языка. [Здесь](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom.tex#L836C3-L837C27) и [здесь](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom.tex#L873C3-L884C32).
- [Добавлена переменная цвета подписей к картинкам/таблицам.](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom.tex#L683C1-L683C89)
- [Добавлена переменная использования **metadata** на титульной странице.](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom.tex#L1017C9-L1017C29)

[*eisvogel-custom_mephi_titlepage.tex*](/templates/eisvogel-custom_mephi_titlepage.tex)
отличается от [*eisvogel-custom.tex:*](/templates/eisvogel-custom.tex)

- Добавлена [титульная страница](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/templates/eisvogel-custom_mephi_titlepage.tex#L1031C5-L1069C8) - пародия на ГОСТ:

| Без логотипа                                                        | С логотипом                                                        |
| ------------------------------------------------------------------- | ------------------------------------------------------------------ |
| [![титульник без логотипа](/title-page-01.jpg)](/title-page-01.jpg) | [![титульник с логотипом](/title-page-02.jpg)](/title-page-02.jpg) |

## Добавление собственных преамбул

В конфигурационном файле [build-conf.toml](/build-conf.toml) есть [настройка вставки собственных преамбул](https://github.com/retrobannerS/latex_template_generator/blob/e5565de0b30b239d3c34c168d4d57dab49b942da/build-conf.toml#L14C1-L16C2). Собственные преамбулы находятся в папке *preambles* и при выполнении [generate_template](/scripts/generate_template.py) помещаются в конец файла **src/template/template.tex**. В проект уже добавлена в качестве демонстрации базовая [преамбула](/preambles/graphics.tex).

Пример вставки [graphics.tex](/preambles/graphics.tex) в конец **src/template/template.tex**:

<img src="/example4.png" alt="example 4" width="800"/>
