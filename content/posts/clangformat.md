+++
date = '2025-08-28T10:02:54+08:00'
draft = false
title = 'Clangformat'
description = 'Clangformat相关规则存档记录'
tags = ['存档']
+++

```toml
# .clang-format

BasedOnStyle: LLVM  # 可选：LLVM, Google, Chromium, Mozilla, WebKit
IndentWidth: 4
TabWidth: 4
UseTab: Never  # 可选：Always, Never, ForIndentation

ColumnLimit: 100  # 每行最大字符数，设为 0 表示不限制
BreakBeforeBraces: Allman  # 可选：Attach, Linux, Stroustrup, Allman, WebKit

AllowShortIfStatementsOnASingleLine: false
AllowShortLoopsOnASingleLine: false
AllowShortFunctionsOnASingleLine: Inline

SpaceAfterCStyleCast: true
SpacesInParentheses: false
SpaceBeforeParens: ControlStatements  # 控制语句前加空格，如 `if (...)`

PointerAlignment: Left  # 可选：Left, Right, Middle

SortIncludes: true
IncludeBlocks: Regroup

# 对齐参数
AlignAfterOpenBracket: Align
AlignConsecutiveAssignments: true
AlignConsecutiveDeclarations: true
AlignOperands: true

# 命名空间格式
NamespaceIndentation: All  # 可选：None, Inner, All

# 结构体/类格式
AccessModifierOffset: -4  # public/private/protected 的缩进

# 注释格式
IndentWrappedFunctionNames: true

```
