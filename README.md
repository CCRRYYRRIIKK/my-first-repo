<h1 align="center">Даниил Пожаров</h1>

---

## Оглавление
- [Обо мне](#обо-мне)
- [Матрица навыков](#матрица-навыков)
- [Цели и план развития](#цели-и-план-развития)
- [Рабочий сетап и сниппеты](#рабочий-сетап-и-сниппеты)
- [Скрытая техническая информация](#скрытая-техническая-информация)
- [Контакты](#контакты)

---

## Обо мне

Меня зовут **Даниил Пожаров**, я студент колледжа при СПБИЭУ (группа ИР11-26-11). Основная система **Gentoo Linux**, собираю ядро и пакеты сам. Каждый день работаю в **Bash**, пишу на **GNU C23** и считаю C своим основным языком.

Я пишу **свой редактор кода** и **эмулятор 8086** - это мои главные пет-проекты. Там я на практике разбираюсь с тем, о чём обычные люди бояться притрагиваться. много думаю про **кэш-линии**, **локальность данных** и **векторизацию** циклов. GDB использую на базовом уровне: точки останова, `backtrace`, просмотр памяти, но постепенно углубляюсь.

> [!TIP]
> Собираю C-код только с `-O2 -march=native -Wall -Wextra -Werror` и проверяю горячие циклы через `perf stat` - так сразу видно, помогает ли векторизация или это просто догадка.[^1]

[^1]: Векторизация — преобразование компилятором скалярных операций в SIMD-инструкции (SSE/AVX/AVX-512) для обработки нескольких элементов данных одной командой.

---

## Матрица навыков

| Направление / Технология | Уровень / Статус | Опыт / Практика |
|:---|:---:|---:|
| **GNU C23** | Отлично, основной язык | редактор, эмулятор, учебные задачи |
| **Linux (Gentoo)** | Уверенно, ежедневно | основная ОС, сборка из исходников |
| **Bash** | Уверенно | скрипты, автоматизация, сборка |
| **GCC** | Уверенно | `-O2`, `-march=native`, предупреждения |
| **Vim** | Постоянно | правка кода и конфигов |
| **GDB** | Базовый | `break`, `bt`, `x/`, `print`, `next` |
| **Git** | Базовый | коммиты, ветки, `push`/`pull` |

---

## Цели и план развития

### 1. Язык и системное программирование

- [x] Перейти на Gentoo как основную систему
- [x] Уверенно писать на GNU C23
- [x] Начать писать собственный редактор кода
- [x] Начать писать эмулятор
- [ ] Полностью понять C# 
- [ ] Довести эмулятор до стабильного прохождения тестов
- [ ] Довести редактор до состояния, в котором можно работать каждый день
- [ ] Разобраться с `mmap`, сигналами и многопоточностью на практике

### 2. Производительность

- [x] Понимать работу кэшей (L1/L2/L3), локальность данных
- [x] Знать теорию векторизации (SSE/AVX)
- [ ] Системно применять `perf`, `valgrind` и `cachegrind`
- [ ] Измерить ускорение горячих циклов в своих проектах и записать результаты

### 3. Учебные и карьерные цели

- [ ] Оформить профиль на GitHub и залить редактор и эмулятор
- [ ] Пройти первое код-ревью
- [ ] Собрать портфолио из 5 завершённых проектов

---

## Рабочий сетап и сниппеты

Так я собираю свои пет-проекты на Gentoo:

```bash
#!/usr/bin/env bash
set -euo pipefail

CC=gcc
CFLAGS="-std=c23 -O3 -march=native -Wall -Wextra -Werror -g"
SRC=$(find src -name '*.c')
OUT=build/my_prog

mkdir -p build
$CC $CFLAGS $SRC -o "$OUT"
```

Фрагмент из моего редактора, вывод файла в терминал:

```c
static inline uint8_t _print_line_number(
	struct Editor editor,
	uint16_t line_position
){
	uint8_t length =  getNumberLength(editor.lines.count);

	set_graphics_attr(ATTR_SET_FG_YELLOW + ATTR_BRIGHT_COLOR);
	printf("%*d ", length, line_position + 1);

	set_graphics_attr(ATTR_RESET);

	return length + 1; // plus one because whitespace
}

void print_empty_line(struct Editor* editor)
{
	set_graphics_attr(ATTR_SET_FG_BLUE + ATTR_BRIGHT_COLOR);
	putchar(editor->settings.empty_line_char);
	set_graphics_attr(ATTR_RESET);
	clear_line_from_cursor();
	putchar('\n');
}

void print_line(struct Editor editor, uint16_t line_position)
{
	if (line_position >= editor.lines.count) {
		print_empty_line(&editor);
		return;
	}

	char* line = editor.lines.data[line_position].chars;

	uint16_t print_length = 0;
	if (editor.settings.show_line_number == true) {
		print_length += _print_line_number(editor, line_position);
	}


	register char ch = *line;

	while (ch != '\0'){
		if (print_length >= (editor.screen_columns - 1)){
			set_graphics_attr(ATTR_SET_BG_RED);
			putchar('>');
			set_graphics_attr(ATTR_RESET);
			putchar('\n');
			return;
		}

		if(ch == '\t'){
			_print_tab(editor.settings.tab_width);
			print_length += editor.settings.tab_width;
			goto next_char;
		}

		print_length++;
		putchar(ch);
		next_char:
			line++;
			ch = *line;
	}
	clear_line_from_cursor();
	putchar('\n');
}

```

Горячие клавиши, которые использую каждый день:

- Выход из режима вставки в Vim: <kbd>ESC</kbd>
- Сохранить и выйти: <kbd>:x</kbd>
- Начало / конец файла: <kbd>gg</kbd> / <kbd>G</kbd>
- Отмена: <kbd>u</kbd>
- Повтор действия: <kbd>Ctrl</kbd> + <kbd>R</kbd>
- Прервать процесс в терминале: <kbd>Ctrl</kbd> + <kbd>C</kbd>
- Очистить экран: <kbd>Ctrl</kbd> + <kbd>L</kbd>
- Поиск по истории команд: <kbd>Ctrl</kbd> + <kbd>R</kbd>

> [!WARNING]
> Не коммитьте мусор сборки (`*.o`, `a.out`, `build/`), локальные конфиги редактора и файлы окружения (`.env`). Добавьте их в `.gitignore` **до** первого `git add .`, иначе эта ошибка останется в истории навсегда

---

## Скрытая техническая информация

<details>
<summary>Нажмите, чтобы посмотреть конфигурацию ноутбука и тестовый лог</summary>

```text
Окружение разработчика:
  Ноутбук:  Asus Vivobook 15
  ОС:       Gentoo Linux
  Ядро:     6.18.39-gentoo-dist-bin
  Shell:    Bash 5.3.15(1)-release 
  Компилятор: GNU gcc (Gentoo 15.3.0 p8) 15.3.0
  Отладчик: GNU gdb (Gentoo 17.2 vanilla) 17.2
  Редактор: Vim
```

```console
$ gcc --version | head -n1
gcc (Gentoo 15.3.0 p8) 15.3.0

$ uname -sr
Linux 6.18.39-gentoo-dist-bin
```

</details>

---

## Контакты

<p>
  <a href="https://github.com/CCRRYYRRIIKK">Мой GitHub</a>
  <a href="mailto:cryrik2008@gmail.com">Моя почта</a>
  <a href="https://fox.g-5.xyz/">Мой вебсайт</a>
</p>

- **Учебное заведение:** СПБИЭУ
- **Группа:** ИР11-26-11
- **Почта:** [cryrik2008@gmail.com](mailto:cryrik2008@gmail.com)
- **GitHub:** https://github.com/CCRRYYRRIIKK

---

<p align="center"><em>Собрано вручную, в Vim, в Gentoo, без мыши.</em></p>
