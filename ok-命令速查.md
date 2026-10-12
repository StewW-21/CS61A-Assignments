# ok 命令速查（CS61A 作业用）

> 自学者专用版：不需要提交、不需要账号，**每条命令都带 `--local`**。
> 最后更新：2026-10-12

---

## 0. 用之前必须知道的四件事

| 事项 | 说明 |
|---|---|
| **必须先 cd 进作业文件夹** | `ok` 是**每个作业文件夹各自带一份**的（`Lab\lab01\ok`、`HW\hw01\ok`…）。在上级目录跑会报 `can't open file '...\ok'` |
| **先保存再测试** | Python 读的是**磁盘上的文件**，不是编辑器缓冲区。VS Code 建议开 `File → Auto Save`，否则"改完了却没变化" |
| **退出码不可信** | ok 的退出码恒为 `0`，即使测试失败。**只看文本输出里的 `passed / failed`** |
| **`--local` 的含义** | "不联网、不提交"。自学者没必要去掉它 |

---

## 1. 常用命令

| 命令 | 作用 | 要你输入吗 |
|---|---|---|
| `python ok --local` | **检验**：跑全部必做题 | 否 |
| `python ok -q digit --local` | 只检验某一题（`-q` = `--question`） | 否 |
| `python ok -q digit -v --local` | 显示**所有**用例结果（默认只显示失败的） | 否 |
| `python ok -q digit -i --local` | 测试失败后**直接进 Python 交互解释器**，现场调试 | 是 |
| `python ok -u --local` | **解锁**"猜输出"题（**不是检验！**） | **是**（要你输答案） |
| `python ok -u -q if-statements --local` | 只解锁某一题 | 是 |
| `python ok --help` | 看完整选项列表 | 否 |
| ~~`python ok --submit`~~ / ~~`--authenticate`~~ | 提交 / 登录——**自学者用不到** | — |

### 典型工作流

```powershell
cd C:\CS61A\CS61A-Assignments\Lab\lab01
python ok -u --local        # 一次性：解锁"猜输出"题（有的话）
python ok --local           # 检验：做题中反复跑这个
python ok -q double_eights --local   # 卡在哪题就单跑哪题
python ok -q double_eights -i --local  # 失败后进 REPL 现场试
```

---

## 2. 怎么读输出

| 输出 | 含义 |
|---|---|
| `N test cases passed! No cases failed.` | ✅ 全过 |
| `0 test cases passed before encountering first failed test case` | ❌ 有失败，下面会跟 `# Error: expected ... but got ...` |
| `Locked: N` + `Use the -u option to unlock them` | 🔒 还有"猜输出"题没解锁，**此时 ok 不会评分** |
| `Cannot backup when running ok with --local.` | 正常提示（本地模式不备份），忽略 |
| `# Error: expected 4 but got None` | 期望 4，你返回了 `None`（典型：用 `print` 代替了 `return`） |

**失败时的排查顺序**：看 `expected` 和 `but got` 的差别 → 回到那一题手动推一遍 → 需要时用 `-i` 进 REPL 试。

---

## 3. `-u` 解锁题长什么样

```
Unlocking tests

At each "? ", type what you would expect the output to be.
Type exit() to quit

>>> welcome()
(line 1)? -- Not quite. Try again! --
(line 1)? 
```

- **逐行问**：`(line 1)?` 就是"第 1 行输出是什么"，答完回车会问 `(line 2)?`
- **答错没有惩罚**，可以反复试
- 输入 `exit()` 中途退出
- 屏幕上的官方提示就是最好的办法：*"try it out in the Python interpreter"* —— 去 REPL 里敲一遍

---

## 4. 三个高频坑（都真踩过）

| 现象 | 原因与解法 |
|---|---|
| `python: can't open file '...\Lab\ok'` | 你人在**上级目录**。`cd` 进作业文件夹再跑 |
| 改完代码测试结果却没变 | **文件没保存**。`Ctrl+S`，并打开 Auto Save |
| `UnboundLocalError: cannot access local variable 'x'` | ① 变量名前后不一致（初始化 `sums`、累加却写 `sum`）；② 用了**内置名**当变量（`sum`/`min`/`max`/`list`/`str`）；③ 函数里赋值让该名字**整个函数**都变本地。**判断"有没有传值"要用 `x == None`，不能用 `if x:`（0 是假值）** |
| 程序卡住不动 | `while` 里忘了更新循环变量（比如 `n//10` 漏了 `n = `） |

---

## 5. 附录 A：doctest 格式（作业 docstring 里的例子是**会跑的测试**）

```
>>> square(2)          ← 必须有 >>>（这是开关）
4                      ← 紧跟其下是"期望输出"
```
- 多行语句用 `...` 续行
- 写在函数 docstring 里
- **不要改动作业 docstring 里的例子**——它是评分依据之一（`lab01.ok` 里 `"lab*.py": "doctest"` 就是这个意思）
- 自己验证：`python -m doctest 文件.py -v`

**`>>> a` 这种"再看一眼变量"的行，断言的是返回值** —— 所以有它的题目**必须 `return`**。

**调试打印不打扰判分**：以 `DEBUG:` 或 `DEBUG ` 开头的行会被 ok 自动过滤（大小写不敏感）：
```python
print("DEBUG:", n, k)
```
⚠️ 但**题目本身要求打印**的行（比如 `divisible_by_k` 要打印倍数）**绝不能**加 `DEBUG:` 前缀。

---

## 6. 附录 B：怎么读 traceback（从下往上三步）

```
Traceback (most recent call last):
  File "temp.py", line 10, in <module>     ← 程序入口（<module> = 模块顶层）
    f("hi")
  File "temp.py", line 2, in f             ← 调用链：谁调了谁
    return g(x + x, x)
  File "temp.py", line 8, in h
    return x + 0                           ← 案发现场
TypeError: ...                             ← 最后一行 = 真正的错误
```

1. **看最后一行**：什么错（类型 + 说明）
2. **看紧邻的 `File/line`**：在你代码的哪一行崩的
3. **往上看 2~3 层**：谁调的、传了什么实参（**根源常在这里**）

| 英文 | 中文 |
|---|---|
| `Traceback (most recent call last)` | 回溯；最近一次调用排最后 |
| `File` / `line N` | 文件 / 第 N 行 |
| `in <module>` | 在模块顶层（不在任何函数里） |
| `in f` | 在函数 `f` 里 |

---

## 7. 状态记录

- 进度表：`进度.md` ｜ 卡点日志：`卡点.md`（同目录）
- 课程存档（讲次 / slides / 视频链接 / 往年考题）：<https://insideempire.github.io/CS61A-Website-Archive/>
- 当前进度：Lab01 必做题 ✅（10/10）｜ 待办：`sum_digits`（选做）、HW01
