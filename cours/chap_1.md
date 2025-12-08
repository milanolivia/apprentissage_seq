
---

# 🎓 Fiche de Révision : Introduction aux MDP (Reinforcement Learning)
**Cours :** Lecture 1 - Markov Decision Processes
**Enseignant :** Rémy Degenne (Inria/CRISTAL)

---

## 1. Concepts Fondamentaux du RL
L'Apprentissage par Renforcement (RL) est une méthode d'apprentissage par **"essais et erreurs"**.

*   **Objectif :** Apprendre à agir dans un environnement stochastique (incertain) inconnu pour maximiser un signal de **récompense** (reward).
*   **La boucle Agent-Environnement :**
    1.  L'agent observe l'**état** ($s_t$).
    2.  Il choisit une **action** ($a_t$).
    3.  L'environnement renvoie une **récompense** ($r_t$) et un **nouvel état** ($s_{t+1}$).
*   **Différence clé :** Contrairement à l'apprentissage supervisé, il n'y a pas de "bonne réponse" donnée, seulement un score (récompense) à maximiser sur le long terme.

---

## 2. Processus de Décision Markovien (MDP)
Un MDP modélise la prise de décision séquentielle. Il est défini par le tuple $(\mathcal{S}, \mathcal{A}, R, P)$.

### Les Composantes
*   **$\mathcal{S}$ (Espace d'états) :** L'ensemble de toutes les situations possibles.
*   **$\mathcal{A}$ (Espace d'actions) :** L'ensemble des choix disponibles pour l'agent.
*   **$R$ (Récompense) :** Distribution de la récompense immédiate.
    *   L'espérance de récompense pour une action $a$ dans un état $s$ est notée :
        $$r(s,a) = \mathbb{E}[R \mid s, a]$$
*   **$P$ (Transitions) :** La dynamique du système (probabilités de passage d'un état à un autre).
    *   Probabilité d'arriver en $s'$ sachant qu'on est en $s$ et qu'on fait l'action $a$ :
        $$p(s' \mid s, a) = \mathbb{P}(s_{t+1}=s' \mid s_t=s, a_t=a)$$

### La Propriété de Markov
Le futur ne dépend que de l'état présent, pas du passé (l'historique est "oublié").
$$\mathbb{P}(s_{t+1} \mid s_t, a_t, s_{t-1}, a_{t-1}, ...) = \mathbb{P}(s_{t+1} \mid s_t, a_t)$$

---

## 3. Les Politiques (Policies)
Une politique $\pi$ définit le comportement de l'agent. C'est une stratégie pour choisir l'action en fonction de l'état.

*   **Notation :** $\pi_t : \mathcal{S} \rightarrow \Delta(\mathcal{A})$ (distribution de probabilité sur les actions).
*   **Types de politiques :**
    *   **Déterministe :** Une seule action choisie par état ($\pi(s) = a$).
    *   **Stochastique :** Une distribution de probabilité sur les actions ($\pi(a|s)$).
    *   **Stationnaire :** La règle ne change pas avec le temps ($\pi$ est constant).
    *   **Non-stationnaire :** La règle change à chaque pas de temps ($\pi_1, \pi_2, \dots$).

*Note : Un MDP muni d'une politique fixe devient une Chaîne de Markov contrôlée.*

---

## 4. Fonctions de Valeur (Value Functions)
La **Valeur** $V^\pi(s)$ estime le gain total espéré si l'on part de l'état $s$ et que l'on suit la politique $\pi$.

Il existe deux formulations principales selon l'horizon de temps :

### A. Horizon Fini ($H$)
Utilisé quand l'épisode a une fin définie (ex: une partie de jeu).
$$V^\pi(s) = \mathbb{E}^\pi \left[ \sum_{t=1}^{H} r_t \mid s_1 = s \right]$$

### B. Horizon Infini avec facteur d'escompte ($\gamma$)
Utilisé pour les processus continus. Le paramètre $\gamma \in (0, 1)$ (discount factor) donne moins d'importance aux récompenses lointaines pour assurer la convergence.
$$V^\pi(s) = \mathbb{E}^\pi \left[ \sum_{t=1}^{\infty} \gamma^{t-1} r_t \mid s_1 = s \right]$$

---

## 5. Optimalité et Équations de Bellman

### Politique Optimale
L'objectif est de trouver une politique $\pi^*$ qui maximise la valeur dans chaque état :
$$V^*(s) = \max_{\pi} V^\pi(s)$$
*Théorème :* Il existe toujours une politique optimale qui est **déterministe**.

### Équation de Bellman (Cas Horizon Fini)
La valeur d'un état dépend de la récompense immédiate et de la valeur de l'état suivant.
Pour une politique déterministe $\pi$ à l'étape $h$ :

$$V^\pi_h(s) = r(s, \pi_h(s)) + \sum_{s' \in \mathcal{S}} p(s' \mid s, \pi_h(s)) V^\pi_{h+1}(s')$$

*(Avec la convention que la valeur à la fin de l'horizon $V_{H+1} = 0$).*

---

## 6. Résumé du problème RL global
Dans un MDP **connu** (on connait $r$ et $p$), on peut calculer la politique optimale par programmation dynamique (planification).

Le véritable **Reinforcement Learning** consiste à trouver cette politique optimale dans un MDP **inconnu**, uniquement en interagissant avec l'environnement (en observant les transitions et les récompenses réelles).