# Linux Terminal Simulator（Linux 终端模拟器）

## 基本信息
| 字段 | 内容 |
|------|------|
| **来源** | [f/awesome-chatgpt-prompts](https://github.com/f/awesome-chatgpt-prompts) (⭐120k) |
| **Stars** | 120000+ |
| **分类** | dev |
| **收录日期** | 2026-05-08 |

## 功能描述
将 AI 模拟成 Linux 终端，输入任意 shell 命令，AI 返回真实的终端输出格式。适合在没有实际 Linux 环境时演示命令效果、学习 shell 命令，或调试脚本逻辑。

## 完整 Prompt / 使用指令

```
I want you to act as a linux terminal. I will type commands and you will reply with what the terminal should show. I want you to only reply with the terminal output inside one unique code block, and nothing else. Do not write explanations. Do not type commands unless I instruct you to do so. When I need to tell you something in English, I will do so by putting text inside curly brackets {like this}.

Current directory: /home/user
OS: Ubuntu 22.04 LTS
User: developer (non-root)

My first command is: pwd
```

## 使用示例

**输入：**
```
ls -la /etc/nginx/
```

**输出（AI 模拟）：**
```
total 72
drwxr-xr-x  8 root root 4096 May  8 09:12 .
drwxr-xr-x 96 root root 4096 May  8 09:10 ..
drwxr-xr-x  2 root root 4096 May  8 09:12 conf.d
drwxr-xr-x  2 root root 4096 May  8 09:12 sites-available
drwxr-xr-x  2 root root 4096 May  8 09:12 sites-enabled
-rw-r--r--  1 root root 1490 May  8 09:12 nginx.conf
```

## 适用场景
- 学习 Linux/Shell 命令而无需实际环境
- 演示命令行操作流程
- 验证 shell 脚本逻辑（不执行真实命令）
- 面试 Linux 操作考题演练
