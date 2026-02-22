# 💸 Tip Time — Tip Calculator (Android Kotlin + Jetpack Compose)

> **Module :** Développement Mobile Android  
> **Étudiante :** Balkiss Doulemi  
> **Classe :** IAM  
> **Date :** 22-02-2026  

---

## 🎯 Présentation du projet

**Tip Time** est une application Android simple qui permet à l’utilisateur de :
- saisir le **montant d’une facture**,
- calculer automatiquement un **pourboire** (15% par défaut),
- afficher le résultat en **format monétaire**.

L’application est développée en **Kotlin** avec **Jetpack Compose**, donc l’interface est **déclarative** :  
➡️ *l’UI dépend de l’état*, et quand l’état change, Compose met à jour l’affichage via la **recomposition**.

✅ **[Capture 1 — Application]** Écran de démarrage (starter) :  
Deux textes visibles : **"Calculate Tip"** et **"Tip Amount: $0.00"** (sans champ de saisie).

---

## 🧩 Fichiers importants du projet

### 1) `res/values/strings.xml`

Ce fichier contient les chaînes utilisées dans l’application (bonne pratique : éviter le texte “en dur” dans le code).

```xml
<resources>
   <string name="app_name">Tip Time</string>
   <string name="calculate_tip">Calculate Tip</string>
   <string name="bill_amount">Bill Amount</string>
   <string name="tip_amount">Tip Amount: %s</string>
</resources>


🔎 Utilisation des ressources string 

Le `%s` dans `tip_amount` permet d’afficher une valeur dynamique (le pourboire calculé).

### ✅ Capture 2 — Code (`strings.xml`)

```xml
<resources>
    <string name="app_name">Tip Time</string>
    <string name="calculate_tip">Calculate Tip</string>
    <string name="bill_amount">Bill Amount</string>
    <string name="tip_amount">Tip Amount: %s</string>
</resources>
```

---

# 🧱 Étape 1 — Starter UI (Interface de départ)

Au départ, `TipTimeLayout()` affiche uniquement :

- un texte **Calculate Tip**
- un texte **Tip Amount: $0.00**
- un `Spacer` pour l’espace
- le tout dans une `Column` (organisation verticale)

### ✅ Capture 3 — Code (Starter Version)

```kotlin
@Composable
fun TipTimeLayout() {
    Column(
        modifier = Modifier.padding(40.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(text = stringResource(R.string.calculate_tip))

        Spacer(modifier = Modifier.height(16.dp))

        Text(text = stringResource(R.string.tip_amount, "$0.00"))
    }
}
```

### ✅ Capture 4 — Application
📸 Screenshot de l’émulateur montrant l’interface sans champ de saisie.

---

# ⌨️ Étape 2 — Ajout du champ de saisie (TextField)

## 2.1 Création du composable `EditNumberField()`

Première version simple avec `TextField`.

### ✅ Capture 5 — Code

```kotlin
@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
    TextField(
        value = "",
        onValueChange = {},
        modifier = modifier
    )
}
```

### ✅ Capture 6 — Application
📸 Screenshot montrant le champ visible (version basique).

---

# 🔁 Étape 3 — Gestion de l’état + recomposition

## ❗ Problème compris

Dans Compose :

- `TextField` affiche uniquement la valeur fournie dans `value`
- Si l’état ne change pas → champ bloqué
- Si l’état n’est pas mémorisé → il se réinitialise

## ✅ Solution : `remember { mutableStateOf("") }`

```kotlin
@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
    var amountInput by remember { mutableStateOf("") }

    TextField(
        value = amountInput,
        onValueChange = { amountInput = it },
        modifier = modifier
    )
}
```

### ✅ Capture 7 — Code
Ligne importante :

```kotlin
var amountInput by remember { mutableStateOf("") }
```

### ✅ Capture 8 — Application
📸 Screenshot montrant que le texte tapé reste affiché.

---

# ✨ Étape 4 — Amélioration UX (Label + Clavier numérique)

Améliorations :

- Label : **Bill Amount**
- `singleLine = true`
- Clavier numérique

```kotlin
TextField(
    value = value,
    onValueChange = onValueChange,
    label = { Text(stringResource(R.string.bill_amount)) },
    singleLine = true,
    keyboardOptions = KeyboardOptions(
        keyboardType = KeyboardType.Number
    ),
    modifier = modifier
)
```

### ✅ Capture 9 — Code
TextField avec label + clavier numérique.

### ✅ Capture 10 — Application
📸 Label "Bill Amount" visible.

### ✅ Capture 11 — Application
📸 Clavier numérique affiché.

---

# 🧮 Étape 5 — Calcul et formatage du pourboire

## 5.1 Fonction `calculateTip()`

```kotlin
private fun calculateTip(
    amount: Double,
    tipPercent: Double = 15.0
): String {
    val tip = tipPercent / 100 * amount
    return NumberFormat.getCurrencyInstance().format(tip)
}
```

### ✅ Capture 12 — Code
Fonction complète avec calcul + format monétaire.

---

## 5.2 Conversion sécurisée String → Double

```kotlin
val amount = amountInput.toDoubleOrNull() ?: 0.0
```

- `toDoubleOrNull()` évite les crash
- `?: 0.0` garantit une valeur par défaut

---

# ⬆️ Étape 6 — State Hoisting (Hissage d’état)

## ❗ Pourquoi ?

`TipTimeLayout()` doit connaître la valeur saisie pour calculer le pourboire.

👉 On déplace l’état dans le parent.

---

## ✅ `EditNumberField()` devient stateless

```kotlin
@Composable
fun EditNumberField(
    value: String,
    onValueChange: (String) -> Unit,
    modifier: Modifier = Modifier
) {
    TextField(
        value = value,
        onValueChange = onValueChange,
        label = { Text(stringResource(R.string.bill_amount)) },
        singleLine = true,
        keyboardOptions = KeyboardOptions(
            keyboardType = KeyboardType.Number
        ),
        modifier = modifier
    )
}
```

### ✅ Capture 14 — Code
Signature avec `value` et `onValueChange`.

---

## ✅ `TipTimeLayout()` possède l’état + calcule + affiche

```kotlin
@Composable
fun TipTimeLayout() {
    var amountInput by remember { mutableStateOf("") }

    val amount = amountInput.toDoubleOrNull() ?: 0.0
    val tip = calculateTip(amount)

    Column(
        modifier = Modifier
            .statusBarsPadding()
            .padding(horizontal = 40.dp)
            .verticalScroll(rememberScrollState())
            .safeDrawingPadding(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(text = stringResource(R.string.calculate_tip))

        EditNumberField(
            value = amountInput,
            onValueChange = { amountInput = it },
            modifier = Modifier
                .padding(bottom = 32.dp)
                .fillMaxWidth()
        )

        Text(text = stringResource(R.string.tip_amount, tip))
    }
}
```

### ✅ Capture 13 — Code
Montre :
- `amountInput`
- conversion sécurisée
- appel `calculateTip`
- affichage dynamique

---

### ✅ Capture 15 — Application
📸 Saisie **100** → Tip Amount mis à jour automatiquement.

### ✅ Capture 16 — Application
📸 Champ vide → Tip affiché à 0 (robustesse).

---

# 🎯 Résultat Final

✔ Gestion d’état avec `remember`  
✔ Recomposition automatique  
✔ State Hoisting  
✔ Conversion sécurisée  
✔ Format monétaire  
✔ UX améliorée  

---

🚀 Projet réalisé avec **Jetpack Compose**
