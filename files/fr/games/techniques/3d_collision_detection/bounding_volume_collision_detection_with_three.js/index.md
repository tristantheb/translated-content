---
title: Détection de collision par volumes englobants avec THREE.js
slug: Games/Techniques/3D_collision_detection/Bounding_volume_collision_detection_with_THREE.js
l10n:
  sourceCommit: 21addd31954b2629ab3e186dacdf7edca813dc7d
---

Cet article montre comment implémenter la **détection de collision entre des boîtes englobantes et des sphères avec la bibliothèque Three.js**. On suppose qu'avant cette lecture vous avez d'abord lu notre article d'introduction [Détection de collision 3D](/fr/docs/Games/Techniques/3D_collision_detection) et que vous avez des connaissances de base sur Three.js.

## Utiliser `Box3` et `Sphere`

Three.js dispose d'objets qui représentent des **volumes mathématiques** et des formes — pour les AABB 3D et les sphères englobantes, nous pouvons utiliser les objets **[`Box3` <sup>(angl.)</sup>](https://threejs.org/docs/#api/math/Box3)** et **[`Sphere` <sup>(angl.)</sup>](https://threejs.org/docs/#api/math/Sphere)**. Une fois instanciés, ils exposent des méthodes permettant d'effectuer des tests d'intersection avec d'autres volumes.

### Instancier des boîtes

Pour créer une **instance de `Box3`**, nous devons fournir les **bornes inférieure et supérieure** de la boîte. En général, nous souhaitons que cette AABB soit «&nbsp;liée&nbsp;» à un objet dans notre monde 3D (comme un personnage). Dans Three.js, les instances de `Geometry` possèdent une propriété `boundingBox` avec des bornes `min` et `max` pour l'objet. Gardez à l'esprit que pour que cette propriété soit définie, vous devez au préalable appeler manuellement `Geometry.computeBoundingBox`.

```js
const knot = new THREE.Mesh(
  new THREE.TorusKnotGeometry(0.5, 0.1),
  new MeshNormalMaterial({}),
);

knot.geometry.computeBoundingBox();
const knotBBox = new Box3(
  knot.geometry.boundingBox.min,
  knot.geometry.boundingBox.max,
);
```

> [!NOTE]
> La propriété `boundingBox` prend la `Geometry` elle-même comme référence, et non la `Mesh`. Ainsi, toutes les transformations telles que l'échelle, la position, etc. appliquées à la `Mesh` seront ignorées lors du calcul de la boîte.

Une alternative plus simple qui corrige le problème précédent consiste à définir ces bornes ultérieurement avec `Box3.setFromObject`, qui calculera les dimensions en tenant compte des **transformations _et_ des maillages enfants** d'une entité 3D.

```js
const knot = new THREE.Mesh(
  new THREE.TorusKnotGeometry(0.5, 0.1),
  new MeshNormalMaterial({}),
);

const knotBBox = new Box3(new THREE.Vector3(), new THREE.Vector3());
knotBBox.setFromObject(knot);
```

### Instancier des sphères

L'instanciation d'objets **`Sphere`** est similaire. Nous devons fournir le centre et le rayon de la sphère, qui peuvent être ajoutés à la propriété `boundingSphere` disponible dans `Geometry`.

```js
const knot = new THREE.Mesh(
  new THREE.TorusKnotGeometry(0.5, 0.1),
  new MeshNormalMaterial({}),
);

const knotBSphere = new Sphere(
  knot.position,
  knot.geometry.boundingSphere.radius,
);
```

Malheureusement, il n'existe pas d'équivalent à `Box3.setFromObject` pour les instances de `Sphere`. Ainsi, si nous appliquons des transformations ou modifions la position de la `Mesh`, nous devons mettre à jour manuellement la sphère englobante. Par exemple&nbsp;:

```js
knot.scale.set(2, 2, 2);
knotBSphere.radius = knot.geometry.radius * 2;
```

### Tests d'intersection

#### Point contre `Box3` / `Sphere`

`Box3` et `Sphere` possèdent toutes deux une méthode **`containsPoint`** pour effectuer ce test.

```js
const point = new THREE.Vector3(2, 4, 7);
knotBBox.containsPoint(point);
```

#### `Box3` contre `Box3`

La méthode **`Box3.intersectsBox`** est disponible pour réaliser ce test.

```js
knotBbox.intersectsBox(otherBox);
```

> [!NOTE]
> Ceci est différent de la méthode `Box3.containsBox`, qui vérifie si une `Box3` en enveloppe une autre _entièrement_.

#### `Sphere` contre `Sphere`

De façon similaire, il existe une méthode **`Sphere.intersectsSphere`** pour effectuer ce test.

```js
knotBSphere.intersectsSphere(otherSphere);
```

#### `Sphere` contre `Box3`

Malheureusement, ce test n'est pas implémenté dans Three.js, mais nous pouvons étendre `Sphere` pour implémenter un algorithme d'[intersection sphère contre AABB](/fr/docs/Games/Techniques/3D_collision_detection).

```js
// étendre THREE.js Sphere pour prendre en charge les tests de collision contre Box3
// nous créons un vecteur en dehors de la portée de la méthode afin
// d'éviter d'instancier un nouveau Vector3 à chaque vérification

THREE.Sphere.__closest = new THREE.Vector3();
THREE.Sphere.prototype.intersectsBox = function (box) {
  // obtenir le point du box le plus proche du centre de la sphère par écrêtage
  THREE.Sphere.__closest.set(this.center.x, this.center.y, this.center.z);
  THREE.Sphere.__closest.clamp(box.min, box.max);

  const distance = this.center.distanceToSquared(THREE.Sphere.__closest);
  return distance < this.radius * this.radius;
};
```

### Démos

Nous avons préparé quelques [démos en direct <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/) pour démontrer ces techniques, avec le [code source <sup>(angl.)</sup>](https://github.com/mozdevs/gamedev-js-3d-aabb) à examiner.

- [Point contre boîte et sphère <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/raw_point.html)
- [Boîte contre boîte et sphère <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/raw_box.html)
- [Sphère contre boîte et sphère <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/raw_sphere.html)

![Un objet nœud, une grande sphère et une petite sphère dans un espace 3D. Trois vecteurs sont tracés sur la petite sphère. Les vecteurs pointent dans les directions des trois axes qui définissent l'espace. Un texte en bas indique&nbsp;: Faites glisser la balle.](screen_shot_2015-10-20_at_15.19.16.png)

## Utiliser `BoxHelper`

En alternative à l'utilisation directe d'objets `Box3` et `Sphere`, Three.js propose un objet utile pour faciliter la gestion des **boîtes englobantes&nbsp;: [`BoxHelper` <sup>(angl.)</sup>](https://threejs.org/docs/#api/helpers/BoxHelper)** (anciennement `BoundingBoxHelper`, qui a été déprécié). Cet assistant prend une `Mesh` et calcule un volume de boîte englobante pour celle-ci (y compris ses maillages enfants). Il en résulte une nouvelle `Mesh` boîte représentant la boîte englobante, qui montre la forme de la boîte et peut être passée à la méthode `setFromObject` vue précédemment afin d'obtenir une boîte englobante correspondant à la `Mesh`.

`BoxHelper` est la méthode **recommandée** pour gérer les collisions 3D avec des volumes englobants dans Three.js. Vous n'aurez pas les tests avec sphère, mais le compromis en vaut la peine.

Les avantages de cet assistant sont&nbsp;:

- Il possède une méthode `update()` qui **redimensionne** sa `Mesh` de boîte englobante si la `Mesh` liée tourne ou change de dimensions, et met à jour sa **position**.
- Il **tient compte des maillages enfants** lors du calcul de la taille de la boîte englobante, de sorte que le maillage d'origine et tous ses enfants sont englobés.
- Nous pouvons facilement déboguer les collisions en **rendant** les `Mesh` que `BoxHelper` crée. Par défaut, elles sont créées avec un matériau `LineBasicMaterial` (un matériau three.js pour dessiner des géométries en fil de fer).

Le principal inconvénient est qu'il **crée uniquement des volumes englobants en boîte**&nbsp;; si vous avez besoin de tests sphères contre AABB, vous devez créer vos propres objets `Sphere`.

Pour l'utiliser, nous devons créer une nouvelle instance de `BoxHelper` et fournir la géométrie et — optionnellement — une couleur qui sera utilisée pour le matériau fil de fer. Nous devons également ajouter l'objet nouvellement créé à la scène `three.js` afin de l'afficher. Nous supposons que notre variable de scène s'appelle `scene`.

```js
const knot = new THREE.Mesh(
  new THREE.TorusKnotGeometry(0.5, 0.1),
  new THREE.MeshNormalMaterial({}),
);
const knotBoxHelper = new THREE.BoxHelper(knot, 0x00ff00);
scene.add(knotBoxHelper);
```

Afin de disposer également de notre boîte englobante réelle `Box3`, nous créons un nouvel objet `Box3` et lui faisons prendre la forme et la position de `BoxHelper`.

```js
const box3 = new THREE.Box3();
box3.setFromObject(knotBoxHelper);
```

Si nous modifions la position, la rotation, l'échelle, etc. de la `Mesh`, nous devons appeler la méthode `update()` pour que l'instance de `BoxHelper` corresponde à sa `Mesh` liée. Nous devons également rappeler `setFromObject` afin que la `Box3` suive la `Mesh`.

```js
knot.position.set(-3, 2, 1);
knot.rotation.x = -Math.PI / 4;
// mettre à jour la boîte englobante pour qu'elle englobe toujours le nœud
knotBoxHelper.update();
box3.setFromObject(knotBoxHelper);
```

Effectuer des **tests de collision** se fait de la même manière que dans la section ci-dessus — nous utilisons notre objet `Box3` comme décrit précédemment.

```js
// boîte contre boîte
box3.intersectsBox(otherBox3);
// boîte contre point
box3.containsPoint(point.position);
```

### Démos

Il existe **deux démos** que vous pouvez consulter sur notre [page de démos en direct <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/). [La première <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/api_point.html) illustre les collisions point contre boîte en utilisant `BoxHelper`. [La seconde <sup>(angl.)</sup>](https://mozdecontregithub.io/gamedev-js-3d-aabb/api_box.html) réalise des tests boîte contre boîte.

![Un objet nœud, un objet sphère et un objet cube dans un espace 3D. Le nœud et la sphère sont englobés par une boîte englobante virtuelle. Le cube intersecte la boîte englobante de la sphère. Un texte en bas indique&nbsp;: Faites glisser le cube. Appuyez sur Échap pour basculer les boîtes englobantes.](screen_shot_2015-10-19_at_12.10.06.png)
