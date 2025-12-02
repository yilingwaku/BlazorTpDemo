# BlazorTpDemo Adventure
Ce projet est un mini-jeu développé en **Blazor WebAssembly (.NET 9)**.  
Le joueur incarne un héros devant choisir une combinaison d’équipements pour affronter un Boss final.  
Chaque équipement possède :
- un **poids**,
- une **puissance**,
- une **description**,
- et une **icône**.

Le héro dispose d’une **capacité maximale** de port.  
L’objectif est de trouver la meilleure combinaison **sans dépasser cette limite**, tout en atteignant **la puissance optimale**.
Si le joueur n’atteint pas exactement la puissance optimale → **défaite**.  
S'il atteint l’optimum → **victoire parfaite** 

---

Vous pouvez télécharger les fichiers depuis Git pour exécuter le jeu, mais il est fortement recommandé de le terminer vous-même en suivant les étapes ci-dessous. Vous y trouverez tout le code nécessaire ainsi que les explications correspondantes.

---

- Création de projet
> dotnet new blazorwasm -o NomProjet
- Exécution :
> dotnet run
- Exécution avec surveillance :
> dotnet watch run

---
### Créez un lien dans la barre de navigation qui mène à la page Aventure.
Ajoutez le code suivant dans ./Layout/NavMenu.razor
```razor
<div class="nav-item px-3">
    <NavLink href="Adventure">
        Adventure
    </NavLink>
</div>
```
---
### Étape 1 : Structure de base du composant
Créez un nouveau composant Blazor Adventure.razor dans "/Pages"

Ajoutez la directive @page "/adventure" en haut du fichier

Créez une structure HTML de base avec un conteneur <div>

---

### Étape 2 : Déclaration de la classe Item
Dans la section @code, définissez une classe interne Item avec les propriétés suivantes :

- Icon (string) : pour l'emoji représentant l'équipement
- Name (string) : nom de l'équipement
- Weight (int) : poids de l'équipement
- Power (int) : puissance de l'équipement
- Desc (string) : description de l'équipement
- IsSelected (bool) : état de sélection

```csharp
private class Item
{
    public string Icon { get; set; } = "";
    public string Name { get; set; } = "";
    public int Weight { get; set; }
    public int Power { get; set; }
    public string Desc { get; set; } = "";
    public bool IsSelected { get; set; }
}
```


---

### Étape 3 : Variables et constantes
Déclarez les variables suivantes :
- MaxCarry (int) = 15 : capacité maximum de portage
- BossPower (int) = 35 : puissance requise pour vaincre le boss
  
  Ici, BossPower est la valeur par défaut.
- BattleResult (string) : message de résultat du combat

---

### Étape 4 : Initialisation des équipements
Créez une liste items de type List<Item> et initialisez-la avec 9 équipements :
- Épée en acier (🗡️) : Poids 5, Puissance 9
- Bouclier du gardien (🛡️) : Poids 6, Puissance 7
- Arc elfique (🏹) : Poids 3, Puissance 6
- Bâton magique (🪄) : Poids 4, Puissance 8
- Pierre de talent (💎) : Poids 2, Puissance 5
- Potion de force (🧪) : Poids 1, Puissance 4
- Marteau nain (🔨) : Poids 7, Puissance 11
- Gants de flamme (🔥) : Poids 2, Puissance 6
- Verglas (❄️) : Poids 1, Puissance 3

```csharp
 private List<Item> items = new(){
  new Item { Icon="🗡️", Name="Épée en acier", Weight=5, Power=9, Desc="Une lame robuste et fiable." },
  new Item { Icon="🛡️", Name="Bouclier du gardien", Weight=6, Power=7, Desc="Très solide mais assez lourd." },
  new Item { Icon="🏹", Name="Arc elfique", Weight=3, Power=6, Desc="Léger et d'une grande précision." },
  new Item { Icon="🪄", Name="Bâton magique", Weight=4, Power=8, Desc="Contient une forte énergie mystique." },
  new Item { Icon="💎", Name="Pierre de talent", Weight=2, Power=5, Desc="Renforce la puissance du héros." },
  new Item { Icon="🧪", Name="Potion de force", Weight=1, Power=4, Desc="Augmente temporairement la puissance." },
  new Item { Icon="🔨", Name="Marteau nain", Weight=7, Power=11, Desc="Lourd mais extrêmement puissant." },
  new Item { Icon="🔥", Name="Gants de flamme", Weight=2, Power=6, Desc="Confèrent une maîtrise du feu." },
  new Item { Icon="❄️", Name="Verglas", Weight=1, Power=3, Desc="Contrôler la glace et la neige" }
};
```

---

### Étape 5 : Propriétés calculées
Créez deux propriétés calculées :
- TotalWeight : somme des poids des équipements sélectionnés
- TotalPower : somme des puissances des équipements sélectionnés:

---

### Étape 6 : Interface utilisateur - Section statistiques
Dans la section HTML, ajoutez :
```razor
<h2> Adventure </h2>

<p>Capacité maximum:<strong>@MaxCarry</strong></p>
<p>Poids sélectionné:<strong>@TotalWeight</strong></p>
<p>Poids restant:<strong>@(MaxCarry - TotalWeight)</strong></p>
<p>Puissance:<strong>@TotalPower</strong></p>

<p style="margin-top:20px;">
  Puissance requise : <strong>@BossPower</strong>
</p>
```
---

### Étape 7 : Interface utilisateur - Tableau des équipements
Créez un tableau HTML avec :
  - En-têtes : Équipement, Poids, Puissance, Description, Choisir
  - Une ligne pour chaque item dans items
      - Chaque ligne doit :
      - Afficher l'icône, le nom, le poids, la puissance et la description
      - Avoir un bouton "Sélectionner" ou "Retirer" selon IsSelected
      - Appliquer la classe CSS selected-row si l'item est sélectionné

```razor
<table border="1" cellpadding="5" style="margin-top:20px" width="100%" table-layout="auto;">
    <thead>
      <tr>
        <th>Équipement</th>
        <th>Poids</th>
        <th>Puissance</th>
        <th>Description</th>
        <th>Choisir</th>
      </tr>
    </thead>

    <tbody>
      @foreach (Item item in items)
      {
        <tr class="@(item.IsSelected ? "selected-row" : "")">
          <td>@item.Icon @item.Name</td>
          <td>@item.Weight</td>
          <td>@item.Power</td>
          <td>@item.Desc</td>
          <td>
            <button @onclick="() => Toggle(item)">
              @(item.IsSelected ? "Retirer" : "Sélectionner")
            </button>
          </td>
        </tr>
      }
    </tbody>
  </table>
```

---

### Étape 8 : Méthode Toggle
Implémentez la méthode Toggle(Item item) qui :
- Inverse la valeur de IsSelected de l'item
- Réinitialise BattleResult à une chaîne vide

```razor
  private void Toggle(Item item)
    {
      item.IsSelected = !item.IsSelected;
      BattleResult = "";
    }
```

---

### Étape 9 : Méthode CheckBattleResult
Implémentez la méthode CheckBattleResult() qui :
- Vérifie d'abord si TotalWeight > MaxCarry
  - Si oui, affiche un message d'erreur
  - Sinon, compare TotalPower avec BossPower
  - Si égaux : message de victoire parfaite
  - Si différents : message de défaite

```razor
  private void CheckBattleResult()
  {
    if (TotalWeight > MaxCarry)
    {
      BattleResult = "Trop lourd ! Le héros ne peut plus bouger.";
      return;
    }

    if (TotalPower == BossPower)
    {
      BattleResult = "Victoire parfaite ! Vous avez trouvé la puissance optimale !";
    }
    else
    {
      BattleResult = $"Puissance insuffisante ({TotalPower} / {BossPower}),vous avez perdu";
    }
  }
```

---

### Étape 10 : Section de résultat
- Ajoutez un bouton "Défier le Boss !" qui appelle CheckBattleResult
- Ajoutez une condition pour afficher BattleResult s'il n'est pas vide

```razor
  <button @onclick="CheckBattleResult" style="font-size:18px;">Défier le Boss !</button>
  @if (!string.IsNullOrEmpty(BattleResult))
  {
    <p>
      @BattleResult
    </p>
  }
```

---

### Fonctionnalité avancée (optionnelle)
Si cela vous intéresse, vous pouvez essayer d'en compléter la méthode ComputeOptimalPower() qui :
- Calcule la puissance maximale possible avec le poids maximum
- Peut être utilisée pour ajuster automatiquement BossPower
Vous trouverez également une solution pour cette méthode dans le fichier git.

---

# IHM2    M2 IC    Zhang Jinyang   Matis Basso



















