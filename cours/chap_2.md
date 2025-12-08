
---

# 🎓 Fiche de Révision : Programmation Dynamique
**Cours :** Lecture 2 - Dynamic Programming
**Enseignant :** Rémy Degenne (Inria/CRISTAL)

---

## 1. Contexte et Objectifs
Dans ce cours, on suppose que le MDP est **connu** (on connaît les modèles de transition $P$ et de récompense $R$). L'objectif est de **calculer** (et non pas encore d'apprendre) la politique optimale $\pi^*$ et sa valeur $V^*$.

L'ensemble des méthodes pour résoudre ces problèmes d'optimisation séquentielle s'appelle la **Programmation Dynamique**.

---

## 2. Cas 1 : Horizon Fini ($H$)
Le processus s'arrête après un nombre de pas $H$ connu. La politique optimale peut être non-stationnaire (dépend du temps $t$).

### Méthode : Induction Arrière (Backwards Induction)
On commence par la fin ($t=H$) et on remonte jusqu'au début.

**1. Évaluation d'une politique $\pi$ :**
Pour calculer $V^\pi_h(s)$, on utilise l'équation de Bellman récursive :
$$V_{h}^{\pi}(s) = r(s,\pi_h(s)) + \sum_{s'} p(s'|s,\pi_h(s)) V_{h+1}^{\pi}(s')$$
*(Convention : $V_{H+1}(s) = 0$)*.

**2. Calcul de la politique optimale :**
On calcule $V^*$ en prenant le max sur les actions à chaque étape :
$$V_{h}^{*}(s) = \max_{a \in \mathcal{A}} \left[ r(s,a) + \sum_{s'} p(s'|s,a) V_{h+1}^{*}(s') \right]$$

*   **Complexité temporelle :** $O(S^2 A H)$
*   **Complexité spatiale :** $O(S H)$

---

## 3. Cas 2 : Horizon Infini avec Escompte ($\gamma$)
Le processus ne s'arrête pas, mais les récompenses futures sont atténuées par $\gamma \in (0,1)$. On cherche une politique **stationnaire** (ne dépend pas de $t$).

### A. Évaluation de Politique (Policy Evaluation)
Pour une politique $\pi$ fixée, $V^\pi$ est solution de l'équation linéaire de Bellman :
$$V^{\pi}(s) = r(s,\pi(s)) + \gamma \sum_{s'} p(s'|s,\pi(s)) V^{\pi}(s')$$

Sous forme matricielle : $V^\pi = r^\pi + \gamma P^\pi V^\pi$.
Deux méthodes de résolution :
1.  **Directe (Inversion matricielle) :** $V^\pi = (I - \gamma P^\pi)^{-1} r^\pi$. (Coûteux si $S$ est grand : $O(S^3)$).
2.  **Itérative :** On applique l'opérateur de Bellman $T^\pi$ de façon répétée : $V_{k+1} = T^\pi(V_k)$.

### B. L'Opérateur de Bellman ($T^\pi$ et $T^*$)
C'est le concept clé qui garantit la convergence des algorithmes.

*   **Opérateur $T^\pi$ (pour une politique donnée) :**
    $$T^{\pi}(V)(s) = r(s,\pi(s)) + \gamma \sum_{s'} p(s'|s,\pi(s)) V(s')$$
*   **Opérateur Optimal $T^*$ (maximisation) :**
    $$T^{*}(V)(s) = \max_{a} \left[ r(s,a) + \gamma \sum_{s'} p(s'|s,a) V(s') \right]$$

**Propriété fondamentale (Théorème du point fixe de Banach) :**
Ces opérateurs sont des **$\gamma$-contractions**. Cela signifie que :
1.  Il existe une **unique** solution (point fixe) $V^*$ telle que $T^*(V^*) = V^*$.
2.  Appliquer l'opérateur de façon répétée converge toujours vers cette solution, peu importe le point de départ $V_0$.

---

## 4. Algorithmes de Résolution (Horizon Infini)

Il existe deux algorithmes principaux pour trouver la politique optimale.

### Algorithme 1 : Value Iteration (VI)
On cherche directement $V^*$ en itérant l'opérateur optimal $T^*$, puis on en déduit la politique.

1.  **Initialiser** $V_0$ arbitrairement.
2.  **Répéter** jusqu'à convergence ($||V_{k+1} - V_k|| < \epsilon$) :
    $$V_{k+1}(s) \leftarrow \max_{a} \left( r(s,a) + \gamma \sum_{s'} p(s'|s,a) V_k(s') \right)$$
3.  **Retourner** la politique "greedy" (gloutonne) par rapport à $V_{final}$.

### Algorithme 2 : Policy Iteration (PI)
On alterne entre évaluer la politique actuelle et l'améliorer.

1.  **Initialiser** une politique $\pi_0$.
2.  **Répéter** jusqu'à ce que la politique ne change plus ($\pi_{k+1} = \pi_k$) :
    *   **Évaluation :** Calculer $V^{\pi_k}$ (par inversion de matrice ou itérations).
    *   **Amélioration :** $\pi_{k+1} \leftarrow \text{greedy}(V^{\pi_k})$.
    $$\pi_{k+1}(s) = \arg\max_{a} \left( r(s,a) + \gamma \sum_{s'} p(s'|s,a) V^{\pi_k}(s') \right)$$
3.  **Retourner** $\pi^*$.

---

## 5. Comparaison VI vs PI

| Caractéristique | Value Iteration (VI) | Policy Iteration (PI) |
| :--- | :--- | :--- |
| **Principe** | Met à jour les valeurs ($V$) directement. | Alterne évaluation complète ($V^\pi$) et mise à jour de $\pi$. |
| **Complexité par itération** | Plus faible ($O(S^2 A)$). | Plus élevée (nécessite une évaluation complète, souvent $O(S^3)$). |
| **Nombre d'itérations** | Élevé (convergence asymptotique). | Faible (converge souvent très vite en nombre d'étapes). |
| **Garantie** | Approche $V^*$ à $\epsilon$ près. | Trouve la politique optimale exacte $\pi^*$ en temps fini. |

---

## 6. Implémentation via Q-Values
Pour éviter de recalculer les sommes $\sum p(s'|s,a)$ lors de l'étape "greedy" ou de l'extraction de la politique, on stocke souvent les **Q-Values** :

$$Q(s,a) = r(s,a) + \gamma \sum_{s'} p(s'|s,a) V(s')$$

*   Relation : $V(s) = \max_a Q(s,a)$.
*   Politique gloutonne : $\pi(s) = \arg\max_a Q(s,a)$.
*   Complexité spatiale : $O(S \times A)$.