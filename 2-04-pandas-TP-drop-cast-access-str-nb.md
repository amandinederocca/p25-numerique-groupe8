---
jupytext:
  cell_metadata_json: true
  encoding: '# -*- coding: utf-8 -*-'
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.3
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# TP on the moon

+++

**Notions intervenant dans ce TP**

* suppression de colonnes avec `drop` sur une `DataFrame`
* suppression de colonne entièrement vide avec `dropna` sur une `DataFrame`
* accès aux informations sur la dataframe avec `info`
* valeur contenues dans une `Series` avec `unique` et `value_counts` 
* conversion d'une colonne en type numérique avec `to_numeric` et `astype` 
* accès et modification des chaînes de caractères contenues dans une colonne avec l'accesseur `str` des `Series`
* génération de la liste Python des valeurs d'une série avec `tolist`
   
**N'oubliez pas d'utiliser le help en cas de problème.**

**Répartissez votre code sur plusieurs cellules**

+++

1. importez les librairies `pandas` et `numpy`

```{code-cell} ipython3
# votre code
import pandas as pd
import numpy as np
```

2. 1. lisez le fichier de données `data/objects-on-the-moon.csv`
   2.  affichez sa taille et regardez quelques premières lignes

```{code-cell} ipython3
# votre code
df=pd.read_csv('data/objects-on-the-moon.csv')
df.shape
df.head()
```

3. 1. vous remarquez une première colonne franchement inutile  
     utiliser la méthode `drop` des dataframes pour supprimer cette colonne de votre dataframe  
     `pd.DataFrame.drop?` pour obtenir de l'aide

```{code-cell} ipython3
df1=df.drop(df.columns[0], axis=1)#AXIS 1 POUR COLONNE
df1.head(2)
```

```{code-cell} ipython3
# votre code
pd.DataFrame.drop?
```

4. 1. appelez la méthode `info` des dataframes (`non-null` signifie `non-nan` i.e. non manquant)
   1. remarquez une colonne entièrement vide

```{code-cell} ipython3
df.info
#colonne size complètement vide
```

5. 1. utilisez la méthode `dropna` des dataframes pour supprimer *en place* les colonnes qui ont toutes leurs valeurs manquantes  
     (on s'interdit un code qui ferait explicitement référence à la colonne `'Size'`)
   2. vérifiez que vous avez bien enlevé la colonne `'Size'`

```{code-cell} ipython3
?pd.Dataframe.dropna
```

```{code-cell} ipython3
df2=df1.dropna(axis=1, how='all')#supprimer les colonnes entièrement vides
```

```{code-cell} ipython3
# votre code
df2.columns#ne renvoie pas size dans la liste
```

6. 1. affichez la ligne d'`index` $88$, que remarquez-vous ?
   2. utilisez la méthode `dropna` des dataframes pour supprimer
      *en place* les lignes qui ont toutes leurs valeurs manquantes
      (et de nouveau sans faire référence à une ligne en particulier)

```{code-cell} ipython3
# votre code
df2.loc[88]
#on remarque qu'il n'y a aucune information renseignée
df3=df2.dropna(how='all', axis=0)
```

```{code-cell} ipython3

```

7. 1. utilisez l'attribut `dtypes` des dataframes pour voir le type de vos colonnes
   2. que remarquez vous sur la colonne des masses ?

+++

### votre code
df3.dtypes
#La colonne des masses est un object au lieu d'etre un flottant

+++

8. 1. utilisez la méthode `unique` des `Series`pour en regarder le contenu de la colonne des masses
   2. que remarquez vous ?

```{code-cell} ipython3
valeurs=df3['Mass (lb)'].unique()#permet d'afficher tous les éléments distincts d'une colonne
print(valeurs)
#on remarque que les masses sont comprises dans des paliers: <18077, >400 etc
```

```{code-cell} ipython3
# votre code
pd.Series.unique?
```

9. 1. conservez la colonne `'Mass (lb)'` d'origine  
      (par exemple dans une colonne de nom `'Mass (lb) orig'`)  
   1. utilisez la fonction `pd.to_numeric` pour convertir  la colonne `'Mass (lb)'` en numérique  
      en remplaçant les valeurs invalides par la valeur manquante (NaN)
   1. naturellement vous vérifiez votre travail en affichant le type de la série `df['Mass (lb)']`
   1. combien y a-t-il de données manquantes dans cette colonne ?

```{code-cell} ipython3
df3['Mass (lb) orig']=df3['Mass (lb)']
df3.head(2)
```

```{code-cell} ipython3
import pandas as pd
df3['Mass (lb)']=pd.to_numeric(df3['Mass (lb)'], downcast='integer', errors='coerce')#coerce transforme toutes les valeurs non numériques en nan
df3.head(3)
```

```{code-cell} ipython3
pd.to_numeric?
```

```{code-cell} ipython3
df3['Mass (lb)'].dtype
```

```{code-cell} ipython3
#compte les données manquantes: rajoute le sum avec deux parenthèses
df3['Mass (lb)'].isna().sum()
#4 valeurs manquantes
```

10. 1. cette solution ne vous satisfait pas, vous ne voulez perdre aucune valeur  
       (même au prix de valeurs approchées)  
    1. vous décidez vaillamment de modifier les `str` en leur enlevant les caractères `<` et `>`  
       afin de pouvoir en faire des entiers
    - *hint:*  
       les `pandas.Series` formées de chaînes de caractères sont du type `pandas` `object`  
       mais elle possèdent un accesseur `str` qui permet de leur appliquer les méthodes python des `str`  
       (comme par exemple `replace`)
        ```python
        df['Mass (lb) orig'].str
        ```
        remplacer les `<` et les `>` par des '' (chaîne vide)
     3. utilisez la méthode `astype` des `Series` pour la convertir finalement en `int`

```{code-cell} ipython3
# votre code
df3['Mass (lb) orig'].str.replace("<","")
```

```{code-cell} ipython3
df3['Mass (lb) orig'].str.replace(">","")
```

```{code-cell} ipython3
valeurs=df3['Mass (lb)'].unique()
print(valeurs)#on a bien supprimé les inférieurs ou égaux
```

```{code-cell} ipython3
pd.Series.str?
```

```{code-cell} ipython3
pd.Series.astype?
```

```{code-cell} ipython3
df3 = df3.dropna(subset=['Mass (lb)'])#IL FAUT ENLEVER LES NA QUI NE PEUVENT SE TRANSFORMER EN ENTIER
```

```{code-cell} ipython3
df3['Mass (lb) orig'].astype(dtype='int64')
```

11. 1. sachant `1 kg = 2.205 lb`  
   créez une nouvelle colonne `'Mass (kg)'` en convertissant les lb en kg  
   arrondissez les flottants en entiers en utilisant `astype`

```{code-cell} ipython3
# votre code
df3['Mass (kg)']=df3['Mass (lb)']/2.205
df3.head()
```

```{code-cell} ipython3
df3['Mass (kg)']=df3['Mass (kg)'].round().astype(int)
df3.head(2)
```

12. 1. Quels sont les pays qui ont laissé des objets sur la lune ?
    2. Combien en ont-ils laissé en pourcentage (pas en nombre) ?  
     *hint:* regardez les paramètres de `value_counts`

```{code-cell} ipython3
# votre code
pays=df3['Country'].unique()
print(pays)
```

```{code-cell} ipython3
df3['Country'].value_counts(normalize=True)
```

```{code-cell} ipython3
pd.value_counts?
```

13. 1. quel est le poids total des objets sur la lune en kg ?
    2. quel est le poids total des objets laissés par les `United States`  ?

```{code-cell} ipython3
# votre code
df3['Mass (kg)'].sum()
```

```{code-cell} ipython3

df3.groupby(['Country'])['Mass (kg)'].sum()
```

14. 1. quel pays a laissé l'objet le plus léger ?  

````{admonition} tip
:class: dropdown tip

voyez les méthodes `Series.idxmin()` et `Series.argmin()`
````

```{code-cell} ipython3
df3['Mass (kg)'].idxmin()#renvoie l'index de la plus petite valeur
```

```{code-cell} ipython3
df3['Mass (kg)'].argmin()#renvoie la position numérique de la plus petite valeur
```

```{code-cell} ipython3
# votre code
df3.loc[63][1]
```

15. 1. y-a-t-il un Memorial sur la lune ?  
     *hint:*  
     en utilisant l'accesseur `str` de la colonne `'Artificial object'`  
     regardez si une des descriptions contient le terme `'Memorial'`
    2. quel est le pays qui a mis ce mémorial ?

```{code-cell} ipython3
pd.Series.str?
```

```{code-cell} ipython3
# votre code
df3['Artificial object'].str.contains('Memorial')#il y a un memorial
```

```{code-cell} ipython3
df3.loc[83][1]
```

16. 1. faites la liste Python des objets sur la lune  
     *hint:* voyez la méthode `tolist()` des séries

```{code-cell} ipython3
df3['Artificial object'].tolist()
```

```{code-cell} ipython3
# votre code
pd.Series.tolist?
```

***
