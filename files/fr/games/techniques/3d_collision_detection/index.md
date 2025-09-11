---
title: Détection de collisions 3D
slug: Games/Techniques/3D_collision_detection
l10n:
  sourceCommit: 21addd31954b2629ab3e186dacdf7edca813dc7d
---

Cet article propose une introduction aux différentes techniques de volumes englobants utilisées pour implémenter la détection de collisions dans des environnements 3D. Des articles de suivi couvriront les implémentations dans des bibliothèques 3D spécifiques.

## Boîtes englobantes alignées sur les axes

Comme pour la détection de collisions 2D, les **boîtes englobantes alignées sur les axes** (AABB) sont l'algorithme le plus rapide pour déterminer si deux entités du jeu se chevauchent ou non. Cela consiste à envelopper les entités du jeu dans une boîte non rotée (donc alignée sur les axes) et à vérifier les positions de ces boîtes dans l'espace de coordonnées 3D pour voir si elles se chevauchent.

![Deux objets 3D non carrés flottant dans l'espace, englobés par des boîtes rectangulaires virtuelles.](screen_shot_2015-10-16_at_15.11.21.png)

La **contrainte d'alignement sur les axes** existe pour des raisons de performance. La zone de chevauchement entre deux boîtes non rotées peut être vérifiée uniquement avec des comparaisons logiques, tandis que des boîtes rotées exigent des opérations trigonométriques supplémentaires, plus lentes à calculer. Si vous avez des entités qui vont tourner, vous pouvez soit modifier les dimensions de la boîte englobante pour qu'elle enveloppe toujours l'objet, soit opter pour un autre type de géométrie englobante, comme les sphères (qui sont invariantes à la rotation). Le GIF animé ci-dessous montre un exemple graphique d'une AABB qui adapte sa taille pour s'ajuster à l'entité en rotation. La boîte change constamment de dimensions pour épouser au plus près l'entité qu'elle contient.

![Nœud en rotation animé montrant la boîte rectangulaire virtuelle qui rétrécit et grandit à mesure que le nœud tourne en son sein. La boîte ne tourne pas.](rotating_knot.gif)

> [!NOTE]
> Consultez l'article [Volumes englobants avec Three.js](/fr/docs/Games/Techniques/3D_collision_detection/Bounding_volume_collision_detection_with_THREE.js) pour voir une implémentation pratique de cette technique.

### Point vs. AABB

Vérifier si un point est à l'intérieur d'une AABB est assez simple — il suffit de vérifier si les coordonnées du point se trouvent dans l'AABB, en considérant chaque axe séparément. Si l'on suppose que _P<sub>x</sub>_, _P<sub>y</sub>_ et _P<sub>z</sub>_ sont les coordonnées du point, et que _B<sub>minX</sub>_–_B<sub>maxX</sub>_, _B<sub>minY</sub>_–_B<sub>maxY</sub>_ et _B<sub>minZ</sub>_–_B<sub>maxZ</sub>_ sont les intervalles de chaque axe de l'AABB, on peut calculer si une collision a eu lieu entre les deux avec la formule suivante&nbsp;:

<!-- prettier-ignore-start -->
<math display="block">
  <semantics><mrow><mi>f</mi><mo stretchy="false">(</mo><mi>P</mi><mo>,</mo><mi>B</mi><mo stretchy="false">)</mo><mo>=</mo><mo stretchy="false">(</mo><msub><mi>P</mi><mi>x</mi></msub><mo>≥</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>X</mi></mrow></msub><mo>∧</mo><msub><mi>P</mi><mi>x</mi></msub><mo>≤</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>X</mi></mrow></msub><mo stretchy="false">)</mo><mo>∧</mo><mo stretchy="false">(</mo><msub><mi>P</mi><mi>y</mi></msub><mo>≥</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>Y</mi></mrow></msub><mo>∧</mo><msub><mi>P</mi><mi>y</mi></msub><mo>≤</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>Y</mi></mrow></msub><mo stretchy="false">)</mo><mo>∧</mo><mo stretchy="false">(</mo><msub><mi>P</mi><mi>z</mi></msub><mo>≥</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>Z</mi></mrow></msub><mo>∧</mo><msub><mi>P</mi><mi>z</mi></msub><mo>≤</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>Z</mi></mrow></msub><mo stretchy="false">)</mo></mrow><annotation encoding="TeX">f(P, B)= (P_x \ge B_{minX} \wedge P_x \le B_{maxX}) \wedge (P_y \ge B_{minY} \wedge P_y \le B_{maxY}) \wedge (P_z \ge B_{minZ} \wedge P_z \le B_{maxZ})</annotation></semantics>
</math>
<!-- prettier-ignore-end -->

Ou en JavaScript&nbsp;:

```js
function isPointInsideAABB(point, box) {
  return (
    point.x >= box.minX &&
    point.x <= box.maxX &&
    point.y >= box.minY &&
    point.y <= box.maxY &&
    point.z >= box.minZ &&
    point.z <= box.maxZ
  );
}
```

### AABB vs. AABB

Vérifier si une AABB croise une autre AABB est similaire au test de point. Il suffit d'effectuer un test par axe, en utilisant les limites des boîtes. Le diagramme ci-dessous montre le test que l'on effectuerait sur l'axe X — en gros, est-ce que les intervalles _A<sub>minX</sub>_–_A<sub>maxX</sub>_ et _B<sub>minX</sub>_–_B<sub>maxX</sub>_ se chevauchent&nbsp;?

![Dessin à la main de deux rectangles montrant le coin supérieur droit de A chevauchant le coin inférieur gauche de B, car la plus grande coordonnée x de A est supérieure à la plus petite coordonnée x de B.](aabb_test.png)

Mathématiquement, cela s'écrit ainsi&nbsp;:

<!-- prettier-ignore-start -->
<math display="block">
  <semantics><mrow><mi>f</mi><mo stretchy="false">(</mo><mi>A</mi><mo>,</mo><mi>B</mi><mo stretchy="false">)</mo><mo>=</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>X</mi></mrow></msub><mo>≤</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>X</mi></mrow></msub><mo>∧</mo><msub><mi>A</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>X</mi></mrow></msub><mo>≥</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>X</mi></mrow></msub><mo stretchy="false">)</mo><mo>∧</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>Y</mi></mrow></msub><mo>≤</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>Y</mi></mrow></msub><mo>∧</mo><msub><mi>A</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>Y</mi></mrow></msub><mo>≥</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>Y</mi></mrow></msub><mo stretchy="false">)</mo><mo>∧</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>Z</mi></mrow></msub><mo>≤</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>Z</mi></mrow></msub><mo>∧</mo><msub><mi>A</mi><mrow><mi>m</mi><mi>a</mi><mi>x</mi><mi>Z</mi></mrow></msub><mo>≥</mo><msub><mi>B</mi><mrow><mi>m</mi><mi>i</mi><mi>n</mi><mi>Z</mi></mrow></msub><mo stretchy="false">)</mo></mrow><annotation encoding="TeX">f(A, B) = (A_{minX} \le B_{maxX} \wedge A_{maxX} \ge B_{minX}) \wedge ( A_{minY} \le B_{maxY} \wedge A_{maxY} \ge B_{minY}) \wedge (A_{minZ} \le B_{maxZ} \wedge A_{maxZ} \ge B_{minZ})</annotation></semantics>
</math>
<!-- prettier-ignore-end -->

Et en JavaScript&nbsp;:

```js
function intersect(a, b) {
  return (
    a.minX <= b.maxX &&
    a.maxX >= b.minX &&
    a.minY <= b.maxY &&
    a.maxY >= b.minY &&
    a.minZ <= b.maxZ &&
    a.maxZ >= b.minZ
  );
}
```

## Sphères englobantes

Utiliser des sphères englobantes pour détecter les collisions est un peu plus complexe que les AABB, mais reste assez rapide à tester. Le principal avantage des sphères est qu'elles sont invariantes à la rotation, donc si l'entité englobée tourne, la sphère englobante reste identique. Leur principal inconvénient est que, à moins que l'entité englobée ne soit réellement sphérique, l'ajustement est généralement médiocre (par exemple, envelopper une personne avec une sphère englobante provoquera de nombreux faux positifs, alors qu'une AABB correspondrait mieux).

### Point vs. sphère

Pour vérifier si une sphère contient un point, il faut calculer la distance entre le point et le centre de la sphère. Si cette distance est inférieure ou égale au rayon de la sphère, le point est à l'intérieur de la sphère.

![Dessin à la main d'une projection 2D d'une sphère et d'un point dans un repère cartésien. Le point est à l'extérieur du cercle, en bas à droite. La distance est indiquée par une ligne en pointillés, étiquetée D, allant du centre du cercle au point. Une ligne plus claire montre le rayon, étiqueté R, allant du centre du cercle au bord du cercle.](point_vs_sphere.png)

En tenant compte du fait que la distance euclidienne entre deux points _A_ et _B_ est <math><semantics><msqrt><mrow><mo stretchy="false">(</mo><msub><mi>A</mi><mi>x</mi></msub><mo>−</mo><msub><mi>B</mi><mi>x</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup><mo>+</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mi>y</mi></msub><mo>−</mo><msub><mi>B</mi><mi>y</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup><mo>+</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mi>z</mi></msub><mo>−</mo><msub><mi>B</mi><mi>z</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup></mrow></msqrt><annotation encoding="TeX">\sqrt{(A_x - B_x)^2 + (A_y - B_y)^2 + (A_z - B_z)^2}</annotation></semantics></math>, notre formule pour la détection de collision point vs. sphère est la suivante&nbsp;:

<!-- prettier-ignore-start -->
<math display="block">
  <semantics><mrow><mi>f</mi><mo stretchy="false">(</mo><mi>P</mi><mo>,</mo><mi>S</mi><mo stretchy="false">)</mo><mo>=</mo><msub><mi>S</mi><mrow><mi>r</mi><mi>a</mi><mi>d</mi><mi>i</mi><mi>u</mi><mi>s</mi></mrow></msub><mo>≥</mo><msqrt><mo stretchy="false">(</mo><msub><mi>P</mi><mi>x</mi></msub><mo>−</mo><msub><mi>S</mi><mi>x</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup><mo>+</mo><mo stretchy="false">(</mo><msub><mi>P</mi><mi>y</mi></msub><mo>−</mo><msub><mi>S</mi><mi>y</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup><mo>+</mo><mo stretchy="false">(</mo><msub><mi>P</mi><mi>z</mi></msub><mo>−</mo><msub><mi>S</mi><mi>z</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup></msqrt></mrow><annotation encoding="TeX">f(P,S) = S_{radius} \ge \sqrt{(P_x - S_x)^2 + (P_y - S_y)^2 + (P_z - S_z)^2}</annotation></semantics>
</math>
<!-- prettier-ignore-end -->

Ou en JavaScript&nbsp;:

```js
function isPointInsideSphere(point, sphere) {
  // nous utilisons des multiplications car c'est plus rapide
  // que d'appeler Math.pow
  const distance = Math.sqrt(
    (point.x - sphere.x) * (point.x - sphere.x) +
      (point.y - sphere.y) * (point.y - sphere.y) +
      (point.z - sphere.z) * (point.z - sphere.z),
  );
  return distance < sphere.radius;
}
```

> [!NOTE]
> Le code ci-dessus comporte une racine carrée, dont le calcul peut être coûteux. Une optimisation simple pour l'éviter consiste à comparer la distance au carré avec le rayon au carré, ainsi l'équation optimisée utiliserait plutôt `distanceSqr < sphere.radius * sphere.radius`.

### Sphère vs. sphère

Le test sphère vs. sphère est similaire au test point vs. sphère. Il s'agit ici de vérifier que la distance entre les centres des sphères est inférieure ou égale à la somme de leurs rayons.

![Dessin à la main de deux cercles partiellement superposés. Chaque cercle (de tailles différentes) a une ligne claire indiquant le rayon, étiquetée R, allant de son centre à son bord. La distance est indiquée par une ligne en pointillés, étiquetée D, reliant les centres des deux cercles.](sphere_vs_sphere.png)

Mathématiquement, cela donne&nbsp;:

<!-- prettier-ignore-start -->
<math display="block">
  <semantics><mrow><mi>f</mi><mo stretchy="false">(</mo><mi>A</mi><mo>,</mo><mi>B</mi><mo stretchy="false">)</mo><mo>=</mo><msqrt><mo stretchy="false">(</mo><msub><mi>A</mi><mi>x</mi></msub><mo>−</mo><msub><mi>B</mi><mi>x</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup><mo>+</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mi>y</mi></msub><mo>−</mo><msub><mi>B</mi><mi>y</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup><mo>+</mo><mo stretchy="false">(</mo><msub><mi>A</mi><mi>z</mi></msub><mo>−</mo><msub><mi>B</mi><mi>z</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup></msqrt><mo>≤</mo><msub><mi>A</mi><mrow><mi>r</mi><mi>a</mi><mi>d</mi><mi>i</mi><mi>u</mi><mi>s</mi></mrow></msub><mo>+</mo><msub><mi>B</mi><mrow><mi>r</mi><mi>a</mi><mi>d</mi><mi>i</mi><mi>u</mi><mi>s</mi></mrow></msub></mrow><annotation encoding="TeX">f(A,B) = \sqrt{(A_x - B_x)^2 + (A_y - B_y)^2 + (A_z - B_z)^2} \le A_{radius} + B_{radius}</annotation></semantics>
</math>
<!-- prettier-ignore-end -->

Ou en JavaScript&nbsp;:

```js
function intersect(sphere, other) {
  // nous utilisons des multiplications car c'est plus rapide
  // que d'appeler Math.pow
  const distance = Math.sqrt(
    (sphere.x - other.x) * (sphere.x - other.x) +
      (sphere.y - other.y) * (sphere.y - other.y) +
      (sphere.z - other.z) * (sphere.z - other.z),
  );
  return distance < sphere.radius + other.radius;
}
```

### Sphère vs. AABB

Tester si une sphère et une AABB sont en collision est légèrement plus compliqué, mais reste simple et rapide. Une approche logique consisterait à vérifier chaque sommet de l'AABB, en effectuant pour chacun un test point vs. sphère. C'est toutefois excessif&nbsp;: tester tous les sommets est inutile, car on peut se contenter de calculer la distance entre le **point le plus proche** de l'AABB (pas nécessairement un sommet) et le centre de la sphère, et vérifier si elle est inférieure ou égale au rayon de la sphère. On peut obtenir cette valeur en contraignant (clamp) le centre de la sphère aux limites de l'AABB.

![Dessin à la main d'un carré chevauchant partiellement le haut d'un cercle. Le rayon est indiqué par une ligne claire, étiquetée R. La ligne de distance va du centre du cercle au point le plus proche du carré.](sphere_vs_aabb.png)

En JavaScript, on ferait ce test ainsi&nbsp;:

```js
function intersect(sphere, box) {
  // obtenir le point de la boîte le plus proche du centre
  // de la sphère par "clamping"
  const x = Math.max(box.minX, Math.min(sphere.x, box.maxX));
  const y = Math.max(box.minY, Math.min(sphere.y, box.maxY));
  const z = Math.max(box.minZ, Math.min(sphere.z, box.maxZ));

  // identique à isPointInsideSphere
  const distance = Math.sqrt(
    (x - sphere.x) * (x - sphere.x) +
      (y - sphere.y) * (y - sphere.y) +
      (z - sphere.z) * (z - sphere.z),
  );

  return distance < sphere.radius;
}
```

## Utiliser un moteur physique

Les **moteurs physiques 3D** fournissent des algorithmes de détection de collisions, la plupart étant également basés sur des volumes englobants. Le fonctionnement d'un moteur physique consiste à créer un **corps physique**, généralement attaché à une représentation visuelle de celui-ci. Ce corps possède des propriétés telles que la vitesse, la position, la rotation, le couple, etc., ainsi qu'une **forme physique**. C'est cette forme qui est prise en compte dans les calculs de détection de collisions.

Nous avons préparé une [démo en direct de détection de collisions <sup>(angl.)</sup>](https://mozdevs.github.io/gamedev-js-3d-aabb/physics.html) (avec le [code source <sup>(angl.)</sup>](https://github.com/mozdevs/gamedev-js-3d-aabb)) que vous pouvez consulter pour voir ces techniques en action — cela utilise le moteur physique 3D open source [cannon.js <sup>(angl.)</sup>](https://github.com/schteppe/cannon.js).

## Voir aussi

Articles liés sur MDN&nbsp;:

- [Détection de collisions avec volumes englobants avec Three.js](/fr/docs/Games/Techniques/3D_collision_detection/Bounding_volume_collision_detection_with_THREE.js)
- [Détection de collisions 2D](/fr/docs/Games/Techniques/2D_collision_detection)

Ressources externes&nbsp;:

- [Tests d'intersection simples pour les jeux <sup>(angl.)</sup>](https://www.gamedeveloper.com/game-platforms/simple-intersection-tests-for-games) sur Game Developer
- [Volume englobant <sup>(angl.)</sup>](https://en.wikipedia.org/wiki/Bounding_volume) sur Wikipédia
