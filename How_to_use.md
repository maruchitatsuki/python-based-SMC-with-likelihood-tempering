<!-- ---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---


# How to Use

## ① このページについて

本ページでは、本プロジェクトの目的と位置づけ、ならびに  
提供しているコードの基本的な利用方法と拡張方法について説明する。

想定する利用者は以下である。

- ODE ベースのモデルに対するパラメータ推定を行いたい利用者
- SMC / MCMC などの確率的推定アルゴリズムを利用する利用者
- 研究・検証用途でコードを再利用・拡張したい利用者

数理モデルや推定アルゴリズムの詳細については、  
Model / Inference / Example 各ページを参照すること。

---

## ② 私たちのコードの利用方法

### 入手方法

本コードは Git リポジトリとして配布されている。  
以下の手順で取得する。

~~~bash
git clone <repository-url>
cd <repository-name>
~~~

必要な Python パッケージは、以下のコマンドでインストールする。

~~~bash
pip install -r requirements.txt
~~~

Jupyter Notebook または Jupyter Book を利用する場合は、  
対応する Python 環境が構築されていることを確認する。

---

### どこをどう変えれば良いのか

本コードは、問題依存部分と共通処理部分が明確に分離されている。自分の問題に適用する際に主に変更が必要となるのは以下の箇所である。

#### モデル定義

- ODE の右辺（反応速度式・力学モデル）
- 状態変数およびモデルパラメータの定義

対象とする力学系に応じて、この部分を書き換えることで  
別のモデルに容易に対応できる。

#### 観測モデル・尤度

- 観測量の定義
- 観測ノイズモデル（例：ガウスノイズ）

観測誤差の構造が異なる場合は、  
尤度関数の定義を適切に変更する必要がある。

#### 推定対象パラメータ

- 推定するパラメータの種類
- 固定するパラメータと推定するパラメータの切り分け

これにより、

- 一部のパラメータのみを推定する
- ノイズパラメータを同時に推定する

といった設定が可能となる。

具体的な変更箇所については、  
Example ページを参照すること。

---

## ③ 拡張

### 並列化について

本コードは、計算コストの高い推定問題を想定し、  
粒子単位・サンプル単位での並列化を前提として設計されている。

- 各粒子の尤度計算は独立
- 並列化により計算時間の短縮が可能

並列化ライブラリ（例：Ray）を用いることで、

- 粒子数の増加
- データ条件数の増加
- 高次元パラメータ空間

にも対応可能である。

---

### 重たい問題を扱う場合の注意点

計算負荷の大きい問題を扱う際には、以下の点に注意する。

- ODE ソルバの設定  
  不要に高精度な設定は計算時間を大きく増加させる。
- パラメータの同定可能性  
  情報量の少ないデータでは、推定が不安定になる可能性がある。
- 並列化オーバーヘッド  
  問題規模によっては、並列化が逆効果になる場合がある。
- メモリ使用量  
  粒子数や保存する中間結果が多い場合、  
  メモリがボトルネックになることがある。

---

## まとめ

本コードは、

- モデル定義を差し替えやすい構造
- 並列計算を前提とした設計
- 小規模問題から大規模問題への拡張性

を備えた、推定問題のための汎用テンプレートである。

Example を起点として、  
自身の問題に合わせて段階的に拡張することを想定している。 -->

---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# How to Use

## 1. About This Page

This page explains the objective and positioning of this project, as well as the basic usage and extension of the provided code.

The intended users are:

- Users who wish to perform parameter estimation for ODE-based models  
- Users who utilize probabilistic inference algorithms such as SMC / MCMC  
- Users who want to reuse or extend the code for research and validation purposes  

For details on the mathematical models and inference algorithms, please refer to the Model / Inference / Example pages.

---

## 2. How to Use Our Code

### Installation

The code is distributed as a Git repository.  
Clone it using the following procedure:

~~~bash
git clone <repository-url>
cd <repository-name>
~~~

Install the required Python packages with:

~~~bash
pip install -r requirements.txt
~~~

If you use Jupyter Notebook or Jupyter Book, ensure that the corresponding Python environment is properly configured.

---

### What Should Be Modified?

The code is structured so that problem-specific components are clearly separated from common components. When adapting it to your own problem, the main parts to modify are:

#### Model Definition

- The right-hand side of the ODE (reaction rate equations or dynamical model)  
- Definitions of state variables and model parameters  

By rewriting this section according to the target dynamical system, the framework can be easily adapted to different models.

#### Observation Model and Likelihood

- Definition of observed quantities  
- Observation noise model (e.g., Gaussian noise)  

If the structure of measurement error differs, the likelihood function must be modified accordingly.

#### Target Parameters for Estimation

- Which parameters to estimate  
- Separation between fixed and estimated parameters  

This enables configurations such as:

- Estimating only a subset of parameters  
- Jointly estimating noise parameters  

For concrete modification examples, refer to the Example page.

---

## 3. Extensions

### Parallelization

This code is designed for computationally intensive estimation problems and assumes parallelization at the particle or sample level.

- Likelihood evaluations for each particle are independent  
- Parallelization can significantly reduce computation time  

By using parallelization libraries (e.g., Ray), the framework can handle:

- Larger numbers of particles  
- Increased data conditions  
- Higher-dimensional parameter spaces  

---

### Notes for Computationally Heavy Problems

When dealing with large-scale or computationally demanding problems, consider the following:

- ODE solver settings  
  Excessively strict tolerances may substantially increase computation time.  

- Parameter identifiability  
  With limited or low-information data, estimation may become unstable.  

- Parallelization overhead  
  Depending on problem size, parallelization may become inefficient.  

- Memory usage  
  A large number of particles or stored intermediate results may cause memory bottlenecks.  

---

## Summary

This code serves as a general template for parameter estimation problems, featuring:

- A modular structure that facilitates model replacement  
- A design assuming parallel computation  
- Scalability from small-scale to large-scale problems  

Users are expected to start from the Example page and extend the framework step by step according to their specific problem.

