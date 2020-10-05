---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Excess hazard is an analogue of relative survival. How come!?"
subtitle: ""
summary: ""
authors: [Enoch Chen]
tags: []
categories: []
date: 2020-10-03T12:46:05+02:00
lastmod: 2020-10-03T12:46:05+02:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
{{% toc %}}

It was mentioned numerous times in different articles that excess hazard is the analogue of relative survival on the hazard scale (1-4). However, how is it derived from?

We first look into the basic definition of relative survival and excess hazard. Relative survival, $R(t)$, is defined as the ratio between the survival of the cancer patient cohort, $S(t)$, and the reference population, $S^*(t)$, whereas excess hazard, $\lambda (t)$, is the all-cause hazard, $h(t)$, minus the expected hazard, $h^*(t)$.
\begin{equation}
R(t) = \frac{S(t)}{S^*(t)}
\end{equation}
\begin{equation}
\lambda (t) = h(t) - h^*(t) 
\end{equation}

### Basics of survival analysis
#### Survival function
To illustrate the relationship in between these two terms, we need to come back to some basics of hazard and survival (5). 
First, in almost every chapter one of any survival analysis book, you can find the definition of cumulative distribution function (c.d.f.) of a time $T$ variable, giving the probablity that the event has occurred as time is less than $t$.

\begin{equation}
F(t)=\operatorname{P}(T<t)
\end{equation}

The complement of this c.d.f. is survival function, , which is a **proportion**, shown as
\begin{equation}
S(t)=\operatorname{P}(T \geq t)=1-F(t)=\int_{t}^{\infty} f(x) d x,
\end{equation}
which is defined as the probability that the event has not occurred by time $t$. That is, for example, the probability that the patient is still alive by time $t$, where $f(t)$ is the probability density function (p.d.f.). And $-f(t)$ is the derivative of $S(t)$.
$$\begin{eqnarray} 
\frac{d S(t)}{dt} &=& \frac{d}{dt} \int_{t}^{\infty} f(x) d x \\\\\\
&=& -f(t)
\end{eqnarray}$$

#### Hazard function
We have to know by heart that the hazard function is the instantaneous rate of occurrence of the event, which is a **rate** (event occurrence per unit of time).

\begin{equation}
h(t)=\lim _{d t \rightarrow 0} \frac{\operatorname{P}(t \leq T<t+d t \mid T \geq t\)}{d t}
\end{equation}

Let us take a look at the numerator itself, which is a conditional probability.
\begin{equation}
\operatorname{P}(t \leq T<t+d t \mid T \geq t\) = \frac{P(t \leq T<t+dt )}{P(T\geq t)},
\end{equation}
where the denominator part is in fact the survival function, $S(t)$, and the numerator part means the probability that the event happens $[t, t+dt)$, which is $f(t)dt$. So we then extend the equation: 
$$\begin{eqnarray} 
\operatorname{P}(t \leq T<t+d t \mid T \geq t\) &=& \frac{P(t \leq T<t+dt )}{P(T\geq t)}   
\\\\\\ &=&\frac{f(t)dt}{S(t)}
\end{eqnarray}$$
Dividing both sides by $dt$, it gives the relationship between the hazard function and the survival function, which is 
$$\begin{eqnarray} 
h(t) &=& \frac{f(t)}{S(t)} \\\\\\
&=& \frac{\frac{-d S(t)}{dt}}{S(t)} \\\\\\
&=& \frac{-{S}'(t)}{S(t)}
&=& \frac{-d \ln S(t)}{dt}
\end{eqnarray} $$
(Please catch up your Calculus 101: $\frac{d \ln x}{dx} = \frac{1}{x}$)

If we rewrite the equation above, we will obtain:
\begin{equation}
\ln S(t) = -\int_{0}^{t} h(x) d x
\end{equation}

$$\begin{eqnarray} 
S(t) &=& exp(-\int_{0}^{t} h(x) d x) \\\\\\
&=& exp(-H(t))
\end{eqnarray}$$

This is pretty! Then we get to know the cumulative hazard function.
#### Cumulative hazard function
The cumulative hazard function is the integral from 0 to time $t$, defined as 
\begin{equation}
H(t)=\int_{0}^{t} h(x) d x.
\end{equation}
Thus, it is easy to understand that the hazard function is the derivative of the cumulative hazard function.
\begin{equation}
h(t) = \frac{d H(t)}{dt}
\end{equation}

### Proof of relative survival
\begin{equation}
R(t) = \frac{S(t)}{S^*(t)}
\end{equation}
First, by defintion of the relationship between the cumulative hazard function and the survival function, we will obtain
\begin{equation}
exp(- \Lambda (t)) = \frac{exp(- H(t))}{exp(-H^*(t))} \\\\\\
\end{equation}
\begin{equation}
exp(- \Lambda (t)) = exp(- H(t) + H^*(t)) 
\end{equation}
\begin{equation}
\Lambda (t) = H(t) - H^*(t)
\end{equation}

Then we got the cumulative excess hazard, $\Lambda (t)$, is the difference between the cumulative all-cause  hazard (of the cancer patient cohort) and the cumulative expected hazard (of the reference population). Then, take the derivative of both sides, shown as:

\begin{equation}
\frac{d \int_{0}^{t} \lambda(x) d x }{dt} = \frac{d \int_{0}^{t} h(x) d x }{dt} - \frac{d \int_{0}^{t} h^*(x) d x }{dt},
\end{equation}
which is exactly,

\begin{equation}
\lambda (t) = h(t) - h^*(t)
\end{equation}

This is fantastic!
Now you know why excess hazard is the hazard analogue of relative survival.

### References
1. Andersson TML, Dickman PW, Eloranta S, Lambe M, Lambert PC. Estimating the loss in expectation of life due to cancer using flexible parametric survival models. Statistics in Medicine 2013;32:5286–5300.
2. Andersson T. Quantifying cancer patient survival: extensions and applications of cure models and life expectancy estimation. Phd thesis, Karolinska Institutet, 2013.
3. Eloranta S. Development and Application of Statistical Methods for Population-Based Cancer
Patient Survival. Phd thesis, Karolinska Institutet, 2013.
4. Eloranta S, Smedby KE, Dickman PW, Andersson TM. Cancer survival statistics for patients and healthcare professionals–a tutorial of real‐world data analysis. Journal of Internal Medicine. 2020.
5. Germán Rodríguez. Generalized Linear Models: Survival Models. https://data.princeton.edu