### CS 320 2025 Exam Source Code

The exam is typeset in Latex. The build using just `main.tex` uses default
parameters for the student info. All non-default builds can be done through the
Makefile.

Default build:
```bash
make
```

Build draft and solution in `out/`:
```bash
make draft
```

Build student copies (uses `test_students.csv`):
```bash
make all
```

Build student copies (uses custom CSV):
```bash
STUDENTS=path/to/students.csv make all
```

All non-default builds use LuaLaTeX and Miller.
