# Instalace WordPressu na Linux (Debian/Ubuntu)

Tento tutoriál popisuje instalaci WordPressu na Linuxový server s **Apache**, **MariaDB/MySQL** a **PHP**. 
Obsahuje také zálohování databáze a souborů.

## 📌 Požadavky
- Debian nebo Ubuntu
- Přístup k terminálu s právy **root**

---

## 🏗️ 1. Instalace Apache a PHP

### **Přepnutí na superuživatele (root)**
```bash
sudo su -
```
Tento příkaz zajistí, že máme potřebná oprávnění pro instalaci.

### **Aktualizace balíčků**
```bash
apt update
```
Aktualizuje seznam dostupných balíčků v systému.

### **Instalace Apache** (webový server)
```bash
apt install apache2 -y
```
Apache je webový server, na kterém poběží WordPress.

### **Instalace PHPMyAdmin** (pro správu databází)
```bash
apt install phpmyadmin
```
Během instalace vybereme:
- **Ne** při výběru databázového serveru (pokud budeme instalovat MySQL/MariaDB zvlášť).
- **Apache2** jako webový server.

### **Kontrola dostupných PHP modulů pro Apache**
```bash
apt search apache2 | grep php
```
Tento příkaz zobrazí seznam PHP modulů pro Apache.

### **Instalace PHP modulu pro Apache**
```bash
apt install libapache2-mod-php -y
```
Tento modul umožní Apache serveru zpracovávat PHP skripty.

---

## 🛢️ 2. Instalace a konfigurace databázového serveru

### **Instalace MySQL nebo MariaDB**
```bash
apt install mariadb-server -y  # nebo mysql-server
```
MariaDB je open-source alternativa MySQL. Obě možnosti fungují stejně.

### **Přihlášení do MySQL**
```bash
mysql
```
Tímto se přihlásíme do databázového serveru jako root.

### **Vytvoření uživatele a databáze**
```sql
CREATE USER 'student'@'localhost' IDENTIFIED BY 'student';
GRANT ALL PRIVILEGES ON *.* TO 'student'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
QUIT;
```
Tento kód:
- Vytvoří uživatele **student** s heslem **student**.
- Přidělí mu plná práva na všechny databáze.
- Aplikujeme změny příkazem `FLUSH PRIVILEGES`.
- Ukončíme MySQL.

---

## 📂 3. Stažení a nastavení WordPressu

### **Přejdeme do složky webového serveru**
```bash
cd /var/www/html
```
Toto je výchozí složka, kde se ukládají webové soubory.

### **Odstranění starých souborů**
```bash
rm -rf *
```
Odstraníme všechny existující soubory.

### **Stažení WordPressu**
```bash
wget "odkaz_na_wordpress.zip"
```
Stáhneme nejnovější verzi WordPressu.

### **Rozbalení archivu**
```bash
unzip "neco.zip"
```
Rozbalíme stažený ZIP soubor.

### **Přesunutí souborů do kořenového adresáře**
```bash
mv wordpress/* ./
```
Soubory z adresáře `wordpress` přesuneme do hlavního webového adresáře.

### **Kontrola uživatele, na kterém běží Apache**
```bash
ps axu | grep apache
```
Tento příkaz zjistí, pod jakým uživatelem běží Apache (obvykle `www-data`).

### **Nastavení správných oprávnění**
```bash
cd ..
chown www-data:www-data -R ./html
```
Tímto zajistíme, že Apache má plný přístup k souborům WordPressu.

---

## 🔧 4. Změna portu Apache

### **Instalace nástroje pro kontrolu otevřených portů**
```bash
sudo apt install net-tools
```

### **Kontrola, na jakém portu běží Apache**
```bash
netstat -vapnl | grep apache
```
Tento příkaz zobrazí seznam portů, které Apache používá.

### **Úprava konfigurace Apache**
```bash
cd /etc/apache2
grep port -r ./*
```
Najdeme soubor s konfigurací portu (např. `ports.conf`).

```bash
mcedit ports.conf
```
V souboru změníme `Listen 80` na jiný port, například `Listen 8080`.

### **Restart Apache**
```bash
systemctl restart apache2
```
Aby se změny projevily, restartujeme webový server.

---

## 🛡️ 5. Zálohování a obnova WordPressu

### **Záloha databáze**
```bash
mysqldump -u student -pstudent wordpress > /tmp/zaloha.sql
```
Tento příkaz vytvoří zálohu databáze WordPress.

### **Obnovení databáze**
```bash
cat /tmp/zaloha.sql | mysql -u student -pstudent wordpress
```
Obnovíme databázi ze zálohy.

### **Záloha souborů WordPressu**
```bash
tar cvfz /tmp/zaloha.tar.gz /var/www/html/
```
Vytvoříme archiv se soubory webu.

### **Obnova souborů WordPressu**
```bash
tar xvfz /tmp/zaloha.tar.gz -C /var/www/html/
```
Obnovíme soubory ze zálohy.

---

## ✅ Závěr
Tento tutoriál pokrývá kompletní instalaci WordPressu, včetně databázového serveru, nastavení oprávnění a zálohování. Po dokončení všech kroků by měl být WordPress dostupný na zvoleném portu serveru. 🚀
