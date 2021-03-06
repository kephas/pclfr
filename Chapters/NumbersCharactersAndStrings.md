# Les nombres, caractères et chaînes de caractères

Bien que les fonctions, les variables, les macros et les 25 opérateurs spéciaux constituent les briques élémentaires du langage lui-même, les briques à la base de vos programmes seront les structures de données que vous utilisez. Comme le faisait remarquer Fred Brooks dans *Le mythe du mois-homme*, « La représentation *est* l'essence de la programmation. »

Common Lisp fournit un support intégré pour la plupart des types de données que l'on trouve habituellement dans les langages modernes : les nombres (entiers, à virgule flottante et complexes), les caractères, les chaînes de caractères, les tableaux (dont les tableaux à plusieurs dimensions), les listes, les tables de hashage, les flux d'entrée et sortie et une abstraction pour représenter portablement des noms de fichiers. Les fonctions sont également un type de donnée de première classe en Lisp -- elles peuvent être stockées dans des variables, passées en arguments, renvoyées comme valeur de retour et créées durant l'exécution.

Et ces types intégrés ne sont qu'un début. Ils sont définis dans le standard du langage pour que les programmeurs puissent compter sur leur présence et parce qu'ils ont tendance à être plus facile à implémenter efficacement lorsqu'ils sont étroitement liés au reste de l'implémentation. Mais comme vous le verrez dans les chapîtres suivants, Common Lisp fournit également plusieurs façons de définir de nouveaux types de données, de définir leurs opérations associées et de les intégrer avec les types de données initiaux.

Mais pour l'instant, vous allez commencer par les types intégrés. Parce que Lisp est un langage de haut niveau, les détails d'implémentation des différents types de données sont en grande partie cachés. De votre point de vue d'utilisateur du langage, les types de données intégrés sont définis par les fonctions qui opèrent dessus. Ainsi, pour apprendre un type de donnée, vous n'avez qu'à en apprendre plus sur les fonctions que vous pouvez utiliser avec. De plus, la plupart des types de données intégrés ont une syntaxe spéciale que le lecteur Lisp comprend et que l'imprimeur Lisp utilise. C'est pourquoi, par exemple, que vous vous pouvez écrire des chaînes comme "foo", des nombres comme 123, 1/23 et 1.23 et des listes comme (a b c). Je décrirai la syntaxe pour les différentes sortes d'objets lorsque je décrirai les fonctions permettant de les manipuler.

Dans ce chapître, je couvrirai les types de données « scalaires » intégrés : les nombres, caractères et chaînes. Techniquement, les chaînes ne sont pas de véritables scalaires -- une chaîne est une séquence de caractères, et vous pouvez accéder aux caractères individuellement et manipuler des chaînes avec une fonction qui opère sur des séquences. Mais je parlerai ici des chaînes parce que la plupart des functions spécifiques aux chaînes les manipulent comme des valeurs individuelles et aussi à cause de l'étroite relation entre plusieurs des functions de chaînes et leur contrepartie pour les caractères.

## Les nombres

Les maths, comme le dit Barbie, sont une chose difficile. Common Lisp ne peut pas rendre le côté maths plus facile, mais il a tendance à nettement moins se mettre en travers du chemin que d'autres langages de programmation. Ce n'est pas une suprise vu son héritage mathématique. Lisp a été initialement conçu par un mathématicien comme un outil pour étudier des fonctions mathématiques. Et un des principaux projets du projet MAC au MIT était le système d'algèbre symbolique Macsyma, écrit en Maclisp, un des prédecesseurs directs de Common Lisp. De plus, Lisp a été utilisé comme un langage d'apprentissage à des endroits comme le MIT, où même les professeurs d'informatique frémissent à l'idée de dire à leurs étudiants que 10/4 = 2, d'où le support dans Lisp des fractions exactes. Et à plusieurs reprises, Lisp a été sollicité pour se mesurer à FORTRAN dans l'arène des calculs numériques à haute performance.

Une des raisons qui fait de Lisp un langage agréable pour les maths est que ses nombres se comportent d'avantage comme de vrais nombres mathématiques que comme les approximations de nombres qui sont faciles à implémenter dans un matériel informatique fini. Par exemple, les entiers dans Common Lisp peuvent presque être arbitrairement grands plutôt que d'être limités par la taille d'un mot machine. Et diviser deux entiers donne une fraction exacte, pas une valeur tronquée. Et comme les fractions sont représentées par des paires d'entiers de taille arbitraire, les fractions peuvent représenter des fractions arbitrairement précises.

D'un autre côté, pour de la programmation numérique à haute performance, vous pourriez vouloir troquer la précision des rationels pour la vitesse offerte par l'utilisation des opérations en virgule flottante propres au matériel. Ainsi, Common Lisp offre également plusieurs types de nombres en virgule flottante, qui sont liés par l'implémentation aux représentations en virgule flottante appropriées supportées par le matériel. Les flottants sont aussi utilisés pour représenter le résultat d'un calcul dont la vraie valeur mathématique serait un nombre irrationnel.

Enfin, Common Lisp supporte les nombres complexes -- les nombres que l'on obtient en faisant des choses tel que prendre les racines carrées ou les logarithmes de nombres négatifs. Le standard Common Lisp va même jusqu'à spécifier les valeurs principales et les branches de coupure pour les fonctions irrationnelles et transcendentales dans le domaine complexe.

## Les nombres littéraux

Vous pouvez écrire des nombrse littéraux de diverses façons ; vous en avez vu quelques exemples au Chapître 4. Néanmoins, il est important de garder en tête la division du travail entre le lecteur Lisp et l'évaluateur Lisp -- le lecteur est chargé de traduire le texte en objets Lisp et l'évaluateur s'occupe alors uniquement de ces objets. Pour un nombre donné d'un type donné, il peut y avoir de nombreuses représentations textuelles différentes, toutes étant traduites dans la même représentation objet par le lecteur Lisp. Par exemple, vous pouvez écrire l'entier 10 comme 10, 20/2, #xA ou d'un tas d'autres manières, mais le lecteur les traduira toutes en le même objet. Quand les nombres sont imprimés en retour -- disons, sur la REPL -- ils sont imprimés dans une syntaxe textuelle canonique qui peut très bien être différente de la syntaxe utilisée pour entrer le nombre. Par exemple :

CL-USER> 10
10
CL-USER> 20/2
10
CL-USER> #xa
10

La syntaxe pour les valeurs entières est un signe optionnel (+ ou -) suivi par un ou plusieurs chiffres. Les fractions sont écrites avec signe optionnel et une séquence de chiffres, représentant le numérateur, une barre oblique (/) et une autre séquence de chiffres représentant le dénominateur. Tous les nombres rationnels sont « canoniqués » lorsqu'ils sont lus -- c'est pourquoi 10 et 20/2 sont tous deux lus comme le même nombre, comme le sont 3/4 et 6/8. Les rationnels sont imprimés dans une forme « réduite » -- les valeurs entières sont imprimées en syntaxe entière et les fractions avec le numérateur et le dénominateur réduit aux plus petits termes.

Il est aussi possible d'écrire des rationnels dans des bases autres que 10. S'il est précédé de #B ou #b, un rationnel littéral est lu comme un nombre binaire avec 0 et 1 comme seuls chiffres légaux. Un #O ou #o indique un nombre octal (chiffres légaux 0-7) et #X ou #x indique un hexadécimal (chiffres légaux 0-F ou 0-f). Vous pouvez écrire des rationnels dans d'autres bases de 2 à 36 avec #nR où *n* est la base (toujours écrit en décimal). Les chiffres « supplémentaires » au-delà de 9 sont pris parmi les lettres A-Z ou a-z. Notez que ces indicateurs de racine s'appliquent à tout le rationnel -- il n'est pas possible d'écrire une fraction avec le numérateur dans une base et le dénominateur dans une autre. Vous pouvez également écrire des valeurs entières, mais pas des fractions, comme des nombres décimaux terminés par un point décimal. Voici quelques exemples de rationnels avec leur représentation décimale, canonique :

123                            ==> 123
+123                           ==> 123
-123                           ==> -123
123.                           ==> 123
2/3                            ==> 2/3
-2/3                           ==> -2/3
4/6                            ==> 2/3
6/3                            ==> 2
#b10101                        ==> 21
#b1010/1011                    ==> 10/11
#o777                          ==> 511
#xDADA                         ==> 56026
#36rABCDEFGHIJKLMNOPQRSTUVWXYZ ==> 8337503854730415241050377135811259267835

Vous pouvez aussi écrire des nombre à virgule flottante de bien des façons. Contrairement aux nombre rationnels, la syntaxe utilisée pour noter un nombre à virgule flottante peut affecter le type du nombre lu. Common Lisp définit quatre sous-types de nombre à virgule flottante : short, single, double et long (NdT : court, simple, double et long). Chaque sous-type peut utiliser un nombre de bits différent pour sa représentation, ce qui signifie que chaque sous-type peut représenter des valeurs qui occupent un intervalle différent et avec un précision différente. Plus de bits permettent un intervalle plus grand et une meilleure précision.

Le format de base pour les nombres à virgule flottante est un signe optionnel suivi par une séquence non-vide de chiffres décimaux comprenant éventuellement un point décimal (NdT : les anglo-saxons utilisent un point à la place de notre virgule). Cette séquence peut être suivie par un marqueur d'exposant de « notation scientifique informatique ». Le marqueur d'exposant consiste en une seule lettre suivie par un signe optionnel et une séquence de chiffres, interprétés comme la puissance de dix par laquelle le nombre avant le marqueur d'exposant devrait être multiplié. La lettre a un double rôle : elle marque le début de l'exposant et indique quelle représentation à virgule flottante doit être utilisée pour le nombre. Les marqueurs d'exposant s, f, d et l (et leur équivalents en majuscule) indiquent respectivement des flottants short, single, double et long. La lettre e indique que la représentation par défaut (initialement single-float) devrait être utilisée.

Les nombres sans marqueur d'exposant sont lus dans la représentation par défaut et doivent contenir un point décimal suivi par au moins un chiffre pour les distinguer des entiers. Les chiffres dans un nombre à virgule flottante sont toujours traités comme des chiffres en base 10 -- les syntaxes #B, #X, #O et #R ne fonctionnent qu'avec des rationels. Voici quelques exemples de nombres à virgule flottante avec leur représentation canonique :

1.0      ==> 1.0
1e0      ==> 1.0
1d0      ==> 1.0d0
123.0    ==> 123.0
123e0    ==> 123.0
0.123    ==> 0.123
.123     ==> 0.123
123e-3   ==> 0.123
123E-3   ==> 0.123
0.123e20 ==> 1.23e+19
123d23   ==> 1.23d+25

Enfin, les nombres complexes sont écrits avec leur propre syntaxe, en l'occurrence #C ou #c suivi par une liste de deux nombres réels représentants les parties réelle et imaginaire du nombre complexe. Il y a en fait cinq genres de nombres complexes parce que les parties réelle et imaginaire doivent être toutes deux rationelles ou toutes deux du même type de nombre à virgule flottante.

Mais vous pouvez les écrire comme vous le voulez -- si un complexe est écrit avec une partie rationelle  et l'autre à virgule flottante, le rationel est converti en un flottant de la représentation appropriée. De même, si les parties réelle et imaginaire sont toutes deux des flottants de représentations différentes, celui dans la plus petite représentation sera *augmenté*.

Néanmoins, aucun nombre complexe n'a de composante réelle rationelle et une partie imaginaire nulle -- dans la mesure où ces valeurs sont, mathématiquement parlant, des rationels, elles sont représentées par la valeur rationelle appropriée. Le même argument mathématique pourrait être employé pour les nombres complexes avec des composantes à virgule flottante, mais, pour ces types complexes, un nombre avec une partie imaginaire nulle est toujours un objet différent du nombre à virgule flottante représentant la composante réelle. Voici quelques exemples de nombres écrits avec la syntaxe des nombres complexes :

#c(2      1)    ==> #c(2 1)
#c(2/3  3/4)    ==> #c(2/3 3/4)
#c(2    1.0)    ==> #c(2.0 1.0)
#c(2.0  1.0d0)  ==> #c(2.0d0 1.0d0)
#c(1/2  1.0)    ==> #c(0.5 1.0)
#c(3      0)    ==> 3
#c(3.0  0.0)    ==> #c(3.0 0.0)
#c(1/2    0)    ==> 1/2
#c(-6/3   0)    ==> -2

## Mathématiques de base

Les opérations arithmétiques de base -- addition, soustraction, multiplication et division -- sont supportées, pour toutes les différentes sortes de nombres Lisp, par les fonctions +, -, * et /. Appeler n'importe laquelle de ces fonctions avec plus de deux arguments est équivalent à appeler la même fonction sur les deux premiers arguments et puis de l'appeler à nouveau sur la valeur de résultat et le reste des arguments. Par exemple, (+ 1 2 3) est équivalent à (+ (+ 1 2) 3). Avec un seul argument, + et * renvoient la valeur, - renvoit son opposé et / son inverse.

(+ 1 2)              ==> 3
(+ 1 2 3)            ==> 6
(+ 10.0 3.0)         ==> 13.0
(+ #c(1 2) #c(3 4))  ==> #c(4 6)
(- 5 4)              ==> 1
(- 2)                ==> -2
(- 10 3 5)           ==> 2
(* 2 3)              ==> 6
(* 2 3 4)            ==> 24
(/ 10 5)             ==> 2
(/ 10 5 2)           ==> 1
(/ 2 3)              ==> 2/3
(/ 4)                ==> 1/4

Si tous les arguments sont du même type de nombre (rationel, à virgule flottante ou complexe), le résultat sera du même type sauf sans le cas où le résultat d'une opération sur des nombres complexes avec des composantes rationelles donne un nombre avec une partie imaginaire nulle, auquel cas le résultat sera un rationel. Néanmoins, les nombres à virgule flottante et complexes sont *contagieux* -- si tous les arguments sont réels mais un ou plus sont des nombres à virgule flottante, les autres arguments sont convertis en la valeur à virgule flottante la plus proche dans la « plus grande » représentation à virgule flottante parmi les arguments effectivement à virgule flottante. Les nombres à virgule flottante dans une réprésentation « plus petite » sont également convertis dans la représentation plus grande. De même, si n'importe lequel des arguments est complexe, tout argument réel est converti en son équivalent complexe.

(+ 1 2.0)             ==> 3.0
(/ 2 3.0)             ==> 0.6666667
(+ #c(1 2) 3)         ==> #c(4 2)
(+ #c(1 2) 3/2)       ==> #c(5/2 2)
(+ #c(1 1) #c(2 -1))  ==> 3

Parce que / ne tronque pas, Common Lisp fournit quatre façons de tronquer et d'arrondir pour convertir un nomre réel (rationel ou à virgule flottante) en un entier : FLOOR tronque vers l'infini négatif et renvoit le plus grand entier inférieur ou égal à l'argument. CEILING tronque vers l'infini positif et renvoit le plus petit entier supérieur ou égal à l'argument. TRUNCATE tronque vers zéro, ce qui le rend équivalent à FLOOR pour les arguments positifs et à CEILING pour les arguments négatifs. Et ROUND arrondi à l'entier le plus proche. Si l'argument est exactement à mi-chemin entre deux entiers, il arrondit à l'entier pair le plus proche.

## Chaînes de caractères

Ainsi qu'il en a été fait mention plus haut, en Common Lisp, les chaînes de caractère sont en fait un type de données composite. En l'occurrence, il s'agit de tableaux de caractères unidimensionnels. Par conséquent, j'évoquerai la plupart des choses qu'on peut réaliser avec des chaînes de caractères dans le prochain chapitre, lorsque je discuterai des nombreuses fonctions permettant de manipuler les séquences, parmi lequelles les chaînes de caractères ne constituent qu'un exemple. Mais les chaînes de caractères ont aussi leur propre syntaxe litérale, ainsi qu'une bibliothèque de fonctions permettant d'effectuer des opérations qui leurs sont spécifiques. Je discuterai de ces aspects particuliers des chaînes de caractères dans le présent chapitre et réserverai les autres au chapitre 11.

Comme vous avez pu le constater, les chaînes littérales sont écrites entre guillemets doubles. Vous pouvez inclure n'importe quel caractère compris dans le jeu de caractères dans une chaîne littérale, mis à part le guillemet double (") et la barre oblique inversée ou backslash (\). Et vous pouvez également inclure ces deux-là si vous les échappez à l'aide du backslash. En fait, le backslash échappe n'importe quel caractère, bien que cela soit ne soit pas nécessaire pour d'autres caractères que le guillemet et le slash inversé lui-même. Le tableau 10-2 montre comment diverses chaînes littérales seront lues par le lecteur Lisp.

Notez qu'en général, la REPL imprimera à l'écran les chaînes de caractères sous une forme lisible, en l'entourant de guillemets doubles et en ajoutant tout backslash nécessaire. Aussi, si vous souhaitez visualiser le contenu d'une chaîne de caractères, vous devrez utiliser une fonction comme FORMAT, permettant d'afficher à l'écran un résultat lisible par l'utilisateur. Par exemple, voici ce que vous verrez si vous entrez dans le REPL une chaîne contenant un guillemet :

CL-USER> "foo\"bar"
"foo\"bar"

FORMAT, en revanche, retournera le contenu véritable de la chaîne de caractères :

CL-USER> (format t "foo\"bar")
foo"bar
NIL

## Comparaison de chaînes de caractères.

Vous pouvez comparer entre elles des chaînes de caractères en utilisant un ensemble de fonctions qui suivent les mêmes conventions d'appelation que celles qui permettent de comparer les caractères eux-mêmes, à ceci près qu'ici STRING est utilisé comme préfixe plutôt que CHAR (voir le tableau 10-3).

Cependant, contrairement aux comparateurs de caractères et de nombres, les comparateurs de chaînes peuvent seulement comparer deux chaînes à la fois. Ceci car ces fonctions peuvent prendre en argument des arguments mot-clefs*(ça sonne mal... je pensais à "peuvent prendre d'autres arguments", tout simplement)*, vous permettant de restreindre la comparaison à des sous-chaînes de l'une ou des deux chaînes considérées.

Les arguments :start1, :end1, :start2 et :end2 spécificient les indices de début (inclus) et de fin (exclus) des sous-chaînes dans la première et la seconde chaînes passées en argument.

Ainsi, l'expression :

(string= "foobarbaz" "quuxbarfoo" :start1 3 :end1 6 :start2 4 :end2 7)

compare la sous-chaîne « bar » dans les deux arguments et renvoie « vrai ». :end1 et :end2 peuvent prendre NIL pour argument (ou l'argument mot-clef peut être tout simplement omis) pour indiquer que la sous-chaîne correspondante s'étend jusqu'à la fin de la chaîne.

Les comparateurs qui renvoient « vrai » lorsque leurs arguments diffèrent, c'est-à-dire, tous à l'exception de STRING= et STRING-EQUAL, renvoient l'indice de la première chaîne pour lequel la différence a été détectée.

(string/= "lisp" "lissome") ==> 3

Si la première chaîne est un préfixe de la seconde, la valeur renvoyée sera la longueur de la première chaîne, c'est-à-dire, le dernier indice valide dans cette chaîne plus un.

(string< "lisp" "lisper") ==> 4

Lorsqu'on compare ensemble des sous-chaînes, la valeur renvoyée n'en demeure pas moins un indice pour la chaîne dans son ensemble. Par exemple, l'expression suivante compare les sous-chaînes « bar » et « baz », mais renvoie 5, car il s'agit-là de l'indice du r dans la première chaîne :

(string< "foobar" "abaz" :start1 3 :start2 1) ==> 5 ; N.B. not 2

D'autres fonctions de chaînes permettent de convertir la casse des chaînes et de supprimer des caractères à l'une ou aux deux extrêmités d'une chaîne. Et, comme je l'ai mentionné plus haut, dans la mesure où les chaînes de caractères sont un type de séquence, toutes les fonctions de séquence dont je discuterai dans le prochain chapitre peuvent être utilisées avec des chaînes. Par exemple, vous pouvez déterminer la longueur d'une chaîne avec la fonction LENGTH. Vous pouvez également récupérer des caractères individuels à partir d'une chaîne à l'aide de la fonction permettant d'accéder à un élément de séquence, ELT, ou de la fonction permettant d'accéder à un élément de tableau, AREF. Vous pouvez encore utiliser un accesseur spécifique aux caractères, CHAR.

Mais ces fonctions, et les autres avec elles, sont le sujet du prochain chapitre, auquel je vous propose de nous rendre sans plus tarder.
