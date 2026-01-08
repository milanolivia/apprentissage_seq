
---

# 📚 Fiche de Révision : Algorithmes d'Apprentissage par Renforcement (Lecture 3)

Cette fiche synthétise le passage de la Programmation Dynamique (modèle connu) aux méthodes d'Apprentissage par Renforcement (modèle inconnu) telles que Monte Carlo, TD Learning, Q-Learning et SARSA.

---

## 1. Introduction & Contexte

En **Apprentissage par Renforcement (RL)**, contrairement à la Programmation Dynamique, l'agent ne connaît pas les paramètres du MDP (les récompenses $r$ et les probabilités de transition $p$).

* **Protocole d'interaction :** L'agent apprend en interagissant avec l'environnement et en observant des transitions $(s_t, a_t, r_t, s_{t+1})$.
* **Objectif :** Estimer la fonction de valeur optimale $V^*$ ou trouver la politique optimale $\pi^*$ à partir de l'historique de ces observations.

---

## 2. Fondements Mathématiques : Approximation Stochastique

Pour apprendre sans modèle, on utilise des méthodes d'estimation itératives basées sur des échantillons.

### A. Estimation Monte-Carlo (MC)
Une méthode naïve pour estimer la valeur $V^\pi(s)$ est de calculer la moyenne des retours cumulés sur plusieurs trajectoires complètes partant de l'état $s$.

* **Formule itérative :** On peut mettre à jour la moyenne progressivement :
    $$\hat{\mu}_n = \hat{\mu}_{n-1} + \frac{1}{n}(Z_n - \hat{\mu}_{n-1})$$
* **Limite :** Il faut attendre la fin de l'épisode (trajectoire complète) pour effectuer une mise à jour.

### B. Algorithme de Robbins-Monro
C'est la base théorique de la convergence des algorithmes de RL. Il s'agit de trouver un point fixe avec des évaluations bruitées via la mise à jour :
$$x_n = x_{n-1} + \alpha_n Y_n$$

* **Conditions de convergence :** Pour garantir la convergence, les pas d'apprentissage (learning rates) $\alpha_n$ doivent satisfaire :
    1.  $\sum \alpha_n = \infty$ (suffisant pour atteindre la cible).
    2.  $\sum \alpha_n^2 < \infty$ (décroissant pour stabiliser la variance).

---

## 3. Temporal Difference (TD) Learning

Le TD Learning combine l'échantillonnage (comme Monte Carlo) et le *bootstrapping* (comme la Programmation Dynamique).

### A. TD(0) : Évaluation de Politique
L'agent met à jour l'estimation $V(s)$ immédiatement après chaque transition $(s, r, s')$, sans attendre la fin de l'épisode.

* **L'erreur TD :** $\delta_k = r_k + \gamma \hat{V}(s_{k+1}) - \hat{V}(s_k)$.
* **Règle de mise à jour :**
    $$V(s) \leftarrow V(s) + \alpha (r + \gamma V(s') - V(s))$$
* **Bootstrapping :** L'estimation est mise à jour en se basant sur une autre estimation ("La valeur de l'estimation est déplacée vers la valeur de la nouvelle estimation").
* **Convergence :** Converge vers $V^\pi$ si les pas respectent les conditions de Robbins-Monro.

### B. TD(1) / Every-visit Monte-Carlo
C'est une extension où l'on met à jour les valeurs de tous les états visités dans une trajectoire à la fin de celle-ci.

---

## 4. Q-Learning : Trouver la Politique Optimale

L'objectif est d'apprendre directement la valeur $Q^*$ (action-état) optimale pour en déduire la politique optimale, sans connaître le modèle.

### A. Algorithme
Q-Learning est un algorithme **Off-policy** (hors politique).

* **Principe :** Il utilise l'équation de Bellman optimale pour la mise à jour.
* **Règle de mise à jour :**
    $$Q(s,a) \leftarrow Q(s,a) + \alpha \left( r + \gamma \max_{b \in \mathcal{A}} Q(s', b) - Q(s,a) \right)$$
* **Convergence :** Converge vers $Q^*$ si toutes les paires $(s,a)$ sont visitées infiniment souvent et si les pas $\alpha$ sont appropriés.

### B. Exploration
L'exploration est cruciale pour visiter tous les états.
* **$\epsilon$-greedy :** Choisit une action aléatoire avec probabilité $\epsilon$, sinon choisit la meilleure action connue.
* **Boltzmann (Softmax) :** Choisit les actions selon une probabilité proportionnelle à leur valeur estimée (température $\tau$).

---

## 5. Actor/Critic et SARSA

L'architecture Actor/Critic distingue l'entité qui agit (Actor) de celle qui évalue l'action (Critic).

### SARSA (State-Action-Reward-State-Action)
C'est une méthode **On-policy** (sur politique). Le critique évalue la politique *actuellement suivie* par l'acteur.

* **Règle de mise à jour :**
    $$\hat{Q}(s_t,a_t) \leftarrow \hat{Q}(s_t,a_t) + \alpha \left( r_t + \gamma \hat{Q}(s_{t+1}, a_{t+1}) - \hat{Q}(s_t,a_t) \right)$$
* **Différence avec Q-Learning :** SARSA utilise l'action $a_{t+1}$ réellement effectuée (qui peut être une action d'exploration), alors que Q-Learning utilise le $\max$ sur toutes les actions possibles.

---

## 6. Comparaison : Q-Learning vs SARSA

L'exemple du "Cliff" (la falaise) illustre la différence de comportement.

| Caractéristique | Q-Learning | SARSA |
| :--- | :--- | :--- |
| **Type** | **Off-policy** | **On-policy** |
| **Cible** | Apprend la valeur de la politique optimale théorique ($\max$). | Apprend la valeur de la politique de comportement (exploratoire). |
| **Comportement** | Emprunte le chemin optimal (au bord de la falaise) mais tombe souvent pendant l'apprentissage. | Emprunte un chemin plus sûr (loin du bord) pour éviter les chutes dues à l'exploration aléatoire. |
| **Convergence** | Converge vers $Q^*$ (optimal). | Converge vers une politique proche de l'optimale si l'exploration ($\epsilon$) diminue. |

---
