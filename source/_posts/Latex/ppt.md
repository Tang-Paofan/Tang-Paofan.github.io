---
title: ppt
mathjax: true
date: 2025-06-04 19:12:57
tags:
categories:
    - Latex
---

记录如何使用latex 写个人汇报ppt
<!-- less -->

```tex
\documentclass{beamer}

% 主题选择: Metropolis (适合学术风格)
\usetheme{metropolis}

% 使用 Times New Roman 字体
\usepackage{mathptmx}

% 语言支持 (适用于中文)
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{graphicx}
\usepackage{booktabs}
\usepackage{xeCJK}  % 中文支持

% 标题、作者和日期
\title{工作报告}
\author{Tangpaofan}
\institute{某甲科技有限公司}
\date{\today}

\begin{document}

  % 标题页
  \begin{frame}
    \titlepage
  \end{frame}
  
  % 目录页
  \begin{frame}{目录}
    \tableofcontents
  \end{frame}
  
  % 第一章
  \section{文档编写}
  \begin{frame}{文档编写}
    已完成：
    \begin{itemize}
      \item 产品使用说明书
    \end{itemize}

  \end{frame}
  

  \section{持续集成}
  \begin{frame}{持续集成}
    选择TeamCity作为持续集成平台，主要原因：
    \begin{itemize}
      \item 易于配置和使用，降低后期维护成本
      \item 支持命令行工具
      \item 支持Docker容器
    \end{itemize}

    \begin{figure}[H]
      \centering
      \includegraphics[width=0.8\textwidth]{image.png}
      \caption{TeamCity持续集成平台}
      \label{fig:teamcity}
    \end{figure}

  \end{frame}

  \section{下周工作计划}
  \begin{frame}{下周工作计划}
    \begin{itemize}
    \end{itemize}
  \end{frame}

  % 结束
  \end{document}
```