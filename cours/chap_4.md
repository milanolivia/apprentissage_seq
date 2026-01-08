Voici une fiche de révision structurée en Markdown pour le cours "Reinforcement Learning with Function Approximation" (Lecture 4), basée sur le document fourni.

---

# 📚 Fiche de Révision : RL avec Approximation de Fonction (Lecture 4)

[cite_start]Cette fiche aborde le passage des méthodes tabulaires (espaces d'états finis et petits) aux méthodes utilisant l'approximation de fonction pour gérer de grands espaces d'états ou des dynamiques inconnues [cite: 16-18].

## 1. Contexte et Motivation

* [cite_start]**Problème des méthodes tabulaires :** Pour de grands espaces d'états (ex : pixels d'un jeu vidéo, robotique, conduite autonome), il est impossible de stocker une valeur pour chaque état $V(s)$ ou chaque paire état-action $Q(s,a)$ en mémoire [cite: 108-114, 200].
* [cite_start]**Solution :** Utiliser une **approximation paramétrique** $V_\theta(s)$ ou $Q_\theta(s,a)$ où $\theta$ est un vecteur de paramètres de taille réduite (ex : poids d'un réseau de neurones) [cite: 201-205].


---

## 2. Choix de la cible : V ou Q ?

* [cite_start]**Apprendre $V^*$ (Value Function) :** Si la dynamique $p(s'|s,a)$ est connue, on peut déduire la politique optimale via $\pi(s) = \text{argmax}_a (r + \gamma \mathbb{E}[V(s')])$[cite: 139].
* [cite_start]**Apprendre $Q^*$ (Action-Value Function) :** Si la dynamique est inconnue (cas standard du RL), il est nécessaire d'apprendre $Q$ pour agir, car la politique greedy est $\pi(s) = \text{argmax}_a Q(s,a)$ [cite: 172-173].
* [cite_start]**Impact de l'erreur :** Si $V$ est une approximation de $V^*$, la perte de performance de la politique induite est bornée par l'erreur d'approximation $||V^* - V||_\infty$[cite: 182].

---

## 3. Types d'Approximation

### A. Approximation Linéaire
La valeur est une combinaison linéaire de **features** (caractéristiques) $\phi(s)$ :
$$V_\theta(s) = \theta^\top \phi(s) = \sum_{i=1}^d \theta_i \phi_i(s)$$
* [cite_start]**Avantage :** Le gradient est simple : $\nabla_\theta V_\theta(s) = \phi(s)$ [cite: 236-241].
* [cite_start]**Exemples de features :** Polynômes, bases de Fourier, RBF (Radial Basis Functions), ou tuiles (Tile coding) [cite: 256-260, 216].

### B. Approximation Non-linéaire
Utilisation de **Réseaux de Neurones** (Deep RL).
* [cite_start]**Avantage :** "Apprennent" les features directement depuis les données brutes (ex: pixels) et sont des approximateurs universels [cite: 267-268].


---

## 4. Évaluation de Politique (Policy Evaluation)

[cite_start]L'objectif est de trouver $\theta$ qui minimise l'erreur quadratique moyenne (MSVE - Mean Square Value Error) par rapport à la vraie valeur $V^\pi$[cite: 297].

### A. Descente de Gradient Stochastique (SGD)
On voudrait faire une mise à jour type gradient :
$$\theta \leftarrow \theta + \alpha (V^\pi(s_t) - V_\theta(s_t)) \nabla_\theta V_\theta(s_t)$$
* [cite_start]**Problème :** On ne connaît pas la cible $V^\pi(s_t)$[cite: 356].

### B. Semi-Gradient TD(0)
[cite_start]On remplace la cible inconnue $V^\pi(s_t)$ par l'estimation bootstrap (TD target) : $r_t + \gamma V_{\theta}(s_{t+1})$[cite: 367].
* **Mise à jour :**
    [cite_start]$$\theta_{t} \leftarrow \theta_{t-1} + \alpha_{t}(r_{t} + \gamma V_{\theta_{t-1}}(s_{t+1}) - V_{\theta_{t-1}}(s_{t})) \nabla_{\theta}V_{\theta_{t-1}}(s_{t})$$[cite: 367].
* [cite_start]**Convergence :** Dans le cas linéaire, converge vers un point fixe $\theta_{TD}$ qui est la solution projetée du point fixe de Bellman [cite: 392-393].

### C. LSTD (Least Square Temporal Difference)
[cite_start]Dans le cas linéaire, au lieu de faire du gradient pas à pas, on peut résoudre directement le système linéaire $A \theta = b$ pour trouver l'optimum [cite: 453-455].
* [cite_start]**Efficacité :** Converge plus vite que TD(0) (meilleure utilisation des données) mais coûte plus cher en calcul ($O(d^2)$ pour inverser la matrice)[cite: 480].

---

## 5. Contrôle (Trouver la politique optimale)

Comment adapter *Policy Iteration* ou *Value Iteration* avec de l'approximation ?

### A. Approximate Dynamic Programming
1.  [cite_start]**LSPI (Least-Squares Policy Iteration) :** Utilise une version LSTD pour estimer $Q^\pi$ (LSTD-Q) à l'intérieur d'une boucle Policy Iteration[cite: 545].
2.  **Fitted-Q Iteration (FQI) :** Une approche offline basée sur *Value Iteration*.
    * [cite_start]À chaque itération $k$, on construit un dataset d'entraînement avec pour cibles $y_i = r_i + \gamma \max_a Q_{k-1}(s'_i, a)$[cite: 597].
    * [cite_start]On entraîne un régresseur (ex: moindres carrés, arbres de décision) pour prédire $y_i$ à partir de $(s_i, a_i)$[cite: 580, 599].

### B. Approximate Q-Learning
Extension directe de Q-Learning avec descente de gradient (semi-gradient).
* **Mise à jour :**
    [cite_start]$$\theta \leftarrow \theta + \alpha (r + \gamma \max_{b} Q_\theta(s', b) - Q_\theta(s, a)) \nabla_\theta Q_\theta(s, a)$$[cite: 710].
* [cite_start]**Risque :** Connu pour diverger si on utilise des approximations non-linéaires (réseaux de neurones)[cite: 716].

---

## 6. Deep Q Networks (DQN)

[cite_start]Pour stabiliser l'apprentissage de Q-Learning avec des réseaux de neurones, DQN introduit plusieurs astuces[cite: 734]:

1.  [cite_start]**Experience Replay (Replay Buffer) :** Stocker les transitions $(s, a, r, s')$ passées dans un tampon et apprendre sur des mini-batchs aléatoires pour casser les corrélations temporelles[cite: 735, 746].
2.  [cite_start]**Target Network :** Utiliser un réseau cible paramétré par $\theta^-$ (copie figée du réseau principal) pour calculer la cible $r + \gamma \max Q_{\theta^-}(s', b)$, mis à jour moins souvent[cite: 747, 792].
3.  [cite_start]**Résultats :** A permis d'atteindre des performances surhumaines sur les jeux Atari[cite: 800].