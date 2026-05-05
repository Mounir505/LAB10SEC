LAB 10 : Guide d'installation de Frida
======================================

🎯 Objectifs
------------

*   Installer Frida (CLI + Python)
*   Déployer `frida-server` sur Android
*   Tester une injection de script
*   Diagnostiquer les problèmes courants

⚙️ Prérequis
------------

*   Python 3.8+
*   ADB installé
*   Android avec débogage USB activé

1️⃣ Installation de Frida
-------------------------

### Installer avec pip

pip install --upgrade frida frida-tools

### Vérification

frida --version
frida-ps --help
python -c "import frida; print(frida.\_\_version\_\_)"
    

2️⃣ Installation ADB
--------------------

adb version
adb devices
    

3️⃣ Déploiement de frida-server
-------------------------------

### Architecture CPU

adb shell getprop ro.product.cpu.abi

### Copie vers Android

adb push frida-server /data/local/tmp/

### Droits

adb shell chmod 755 /data/local/tmp/frida-server

### Lancement

adb shell /data/local/tmp/frida-server &

### Vérification

adb shell ps | grep frida

4️⃣ Test de connexion
---------------------

frida-ps -U
frida-ps -Uai
    

5️⃣ Script Java (hello.js)
--------------------------

Java.perform(function () {
    console.log("\[+\] Frida OK");
});
    

### Injection

frida -U -f com.example.app -l hello.js

6️⃣ Hook natif (hello\_native.js)
---------------------------------

console.log("Script chargé");

Interceptor.attach(Module.getExportByName(null, "recv"), {
    onEnter: function(args) {
        console.log("recv appelée");
    }
});
    

frida -U -n "process" -l hello\_native.js

7️⃣ Commandes utiles Frida
--------------------------

Process.arch
Process.enumerateModules()
Java.available
Process.enumerateThreads()
    

8️⃣ Hooks réseau
----------------

Interceptor.attach(Module.getExportByName(null, "connect"), {
    onEnter: function(args) {
        console.log("Connexion réseau");
    }
});
    

9️⃣ Hooks fichiers
------------------

Interceptor.attach(Module.getExportByName(null, "open"), {
    onEnter: function(args) {
        console.log("Fichier ouvert");
    }
});
    

⚠️ Problèmes courants
---------------------

*   Version Frida ≠ frida-server
*   ADB unauthorized
*   Architecture incorrecte
*   Permissions manquantes

✅ Résultat attendu
------------------

*   Frida fonctionne
*   Connexion établie
*   Scripts injectés avec succès
*   Hooks opérationnels

📘 Exercices pratiques (à livrer)
---------------------------------

### 1️⃣ Installation et preuve

Commandes exécutées :

frida --version
frida-ps --version
python -c "import frida; print(frida.\_\_version\_\_)"
adb devices
    

**Captures d’écran :**

*   frida --version :
<img width="466" height="214" alt="image" src="https://github.com/user-attachments/assets/9aa81380-16c8-4bdd-8975-46bb5266b613" />

*   frida-ps --version :
<img width="616" height="258" alt="image" src="https://github.com/user-attachments/assets/f6cb6213-4d0f-4edd-8d60-adac3a2a3917" />

*   adb devices :
<img width="587" height="300" alt="image" src="https://github.com/user-attachments/assets/b4192e7f-73ee-4811-a985-0ca46eeedbab" />


### 2️⃣ Déploiement Android

Commande utilisée :

adb shell /data/local/tmp/frida-server -l 0.0.0.0
    

**Capture d’écran :**

<img width="1623" height="438" alt="image" src="https://github.com/user-attachments/assets/d7cefe3a-69a0-4b0e-bfed-e1f828877e99" />


Liste des applications :

frida-ps -Uai
    

**Capture :**

<img width="1849" height="1092" alt="image" src="https://github.com/user-attachments/assets/9a12f0dd-3e1b-4275-8fe9-1a7ca761fcfe" />


### 3️⃣ Injection

Fichier `hello.js` :
```Java Script :
Java.perform(function () {
    console.log("\[+\] Frida Java.perform OK");
});
```    

```Commande :

frida -U -f com.example.app -l hello.js
```  

**Capture d’écran :**

<img width="2303" height="610" alt="image" src="https://github.com/user-attachments/assets/6ded2bae-ddf4-44dd-9e11-b16f070579bf" />

### 4️⃣ Dépannage

**Simulation d’erreur :**

*   Arrêt de `frida-server`
*   Erreur lors de `frida-ps -U`

**Diagnostic :**

*   Vérification avec `adb shell ps`
*   Absence du processus frida-server

**Correction :**

adb shell /data/local/tmp/frida-server &
    
