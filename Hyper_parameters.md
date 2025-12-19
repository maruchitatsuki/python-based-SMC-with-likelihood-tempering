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

# Hyper parameters

---

## このページについて

本節では、本研究で用いた Sequential Monte Carlo（SMC）アルゴリズムにおける  
ハイパーパラメータを整理し、それぞれがアルゴリズムの安定性・効率・精度において果たす役割をまとめる。

---

## 主要なハイパーパラメータ

特に重要なハイパーパラメータは  
{ref}`ESS_limit <HP_main_ESS_limit>`（$ESS_{\mathrm{limit}}$），  
{ref}`r_threshold <HP_main_r_threshold>`（$r_{\mathrm{threshold}}$），  
{ref}`N_particle <HP_main_N_particle>`（$N_{\mathrm{particle}}$）  
の3つであり、サンプリング精度及びサンプリング時間に大きな影響を及ぼす。

(HP_main_ESS_limit)=
### $ESS_{\mathrm{limit}}$

$(0,1)$ の範囲をとる。  
$ESS_{\mathrm{limit}}$ は {ref}`ESS <SMC_main_ESS>` と密接に関係するハイパーパラメータである。  
$ESS_{\mathrm{limit}}$ を大きくするほど、推定は速く進行するが精度は落ちる。

(HP_main_r_threshold)=
### $r_{\mathrm{threshold}}$

$(0,1]$ の範囲をとる。  
mutation ステップの終了条件を決める。  
どれだけの粒子が提案パラメータを受け入れたのかを計算し、被採択粒子数 $r_{\mathrm{ac}}$ を求める。  
そして  
\[
\frac{r_{\mathrm{ac}}}{N_p} > r_{\mathrm{threshold}}
\]
であれば、十分にかき混ぜられたと考え、次のステップに進む。

(HP_main_N_particle)=
### $N_{\mathrm{particle}}$

粒子数を表す。  
各粒子はパラメータ空間上の候補点を表し、これらの集合によって事後分布が近似される。  
粒子数を増やすことで分布近似の精度は向上するが、計算コストも増加する。

---

## 補足的なハイパーパラメータ

(HP_sub_mhstep_factor)=
### $MHstep_{\mathrm{factor}}$

Metropolis–Hastings（MH）法における提案分布のスケール係数。  
パラメータ更新時のステップサイズを制御する。

(HP_sub_mhstep_factor_cov)=
### $MHstep_{\mathrm{factor\_cov}}$

粒子分布の共分散行列を用いた MH 提案分布に対するスケール係数。  
粒子間の相関構造をどの程度反映するかを制御する。

(HP_sub_ad_mhstep_num)=
### $N_{\mathrm{MH}}^{\mathrm{adaptive}}$

$\gamma = 1$ の場合のミューテーションステップの反復回数。  
受理率や粒子分布の情報を用いて、提案分布を調整する段階で使用される。

(HP_sub_mhstep_num)=
### $N_{\mathrm{MH}}$

通常のミューテーションステップの反復回数。  
受理率や粒子分布の情報を用いて、提案分布を調整する段階で使用される。

(HP_sub_mhstep_ratio)=
### $MHstep_{\mathrm{ratio}}$

MH 法における提案分布の広さを制御する係数。  
mutation ステップにおける探索戦略の比重を調整する。

(HP_sub_r_threshold_f)=
### $r_{\mathrm{threshold}}^{\mathrm{final}}$

$\gamma = 1$ における $r_{\mathrm{threshold}}$ を決める。  
通常の $r_{\mathrm{threshold}}$ よりは大きい。

(HP_sub_r_threshold_min)=
### $r_{\mathrm{threshold}}^{\mathrm{min}}$

採択率がこのパラメータより小さい場合、提案分布を狭める。

(HP_sub_d_gamma_max)=
### $\Delta \gamma_{\max}$

{ref}`$\gamma$（tempering factor） <SMC_main_γ>` の  
1 ステップあたりの最大増分。  
分布が急激に変化するのを防ぎ、縮退を抑制する役割を持つ。

(HP_sub_gm_reduction_itr)=
### $\gamma_{\mathrm{reduction\_itr}}$

$\Delta \gamma$ の縮小を試行する最大反復回数。  
tempering ステップが過度に細分化されることを防ぐための制限値である。

(HP_sub_gm_reduction_rate)=
### $\gamma_{\mathrm{reduction\_rate}}$

$\Delta \gamma$ を動的に減少させる際の減衰率。  
ESS などの安定性条件が満たされない場合に、  
$\Delta \gamma$ を段階的に小さくするために用いられる。
