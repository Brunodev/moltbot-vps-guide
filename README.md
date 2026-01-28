# Moltbot na VPS - Kompletny Poradnik

Jak postawić własnego AI asystenta (Moltbot) na serwerze VPS z interfejsem graficznym.

---

## Film na YouTube

**[PLACEHOLDER - LINK DO FILMU]**

[![YouTube](https://img.shields.io/badge/YouTube-Film-red?style=for-the-badge&logo=youtube)](LINK_DO_FILMU)

---

## Sponsor

Ten poradnik powstał we współpracy z **[Hostinger](https://www.hostinger.pl/)**.

[![Hostinger](https://img.shields.io/badge/Sponsor-Hostinger-673DE6?style=for-the-badge)](https://www.hostinger.pl/)

---

## Spis treści

- [Wymagania](#wymagania)
- [Część 1: Konfiguracja VPS](#część-1-konfiguracja-vps)
- [Część 2: Instalacja GUI i VNC](#część-2-instalacja-gui-i-vnc)
- [Część 3: Instalacja Moltbot](#część-3-instalacja-moltbot)
- [Część 4: Przeglądarka (opcjonalnie)](#część-4-przeglądarka-opcjonalnie)
- [Bezpieczeństwo](#bezpieczeństwo)
- [Przydatne komendy](#przydatne-komendy)
- [Problemy i rozwiązania](#problemy-i-rozwiązania)

---

## Wymagania

- Serwer VPS z Ubuntu (20.04 / 22.04 / 24.04)
- Minimum 2GB RAM (zalecane 4GB+)
- Dostęp SSH do serwera
- Klient VNC (np. [RealVNC](https://www.realvnc.com/), [TigerVNC](https://tigervnc.org/))

---

## Część 1: Konfiguracja VPS

### 1.1 Połącz się przez SSH

```bash
ssh -i twoj-klucz.pem ubuntu@TWOJ_IP_SERWERA
```

> Jeśli używasz Hostingera, klucz SSH możesz wygenerować w panelu hPanel.

### 1.2 Zaktualizuj system

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Część 2: Instalacja GUI i VNC

### 2.1 Zainstaluj środowisko graficzne XFCE

XFCE jest lekkie i idealne dla serwerów VPS.

```bash
sudo apt install -y xfce4 xfce4-goodies
```

### 2.2 Zainstaluj serwer VNC

```bash
sudo apt install -y tightvncserver
```

### 2.3 Skonfiguruj VNC

```bash
# Pierwsze uruchomienie - ustaw hasło
vncserver :1

# Zatrzymaj serwer
vncserver -kill :1

# Utwórz plik konfiguracyjny
echo '#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &' > ~/.vnc/xstartup

chmod +x ~/.vnc/xstartup

# Uruchom z odpowiednią rozdzielczością
vncserver :1 -geometry 1920x1080 -depth 24
```

### 2.4 Otwórz port w firewallu

**AWS (Security Groups):**
- Typ: Custom TCP
- Port: 5901
- Źródło: Twoje IP
- Kierunek: **Inbound**

**Hostinger / inny VPS:**
```bash
sudo ufw allow 5901/tcp
```

### 2.5 Połącz się przez VNC

W kliencie VNC wpisz:
```
TWOJ_IP_SERWERA:5901
```

---

## Część 3: Instalacja Moltbot

> Oficjalna dokumentacja: **[docs.molt.bot](https://docs.molt.bot/)**

### 3.1 Zainstaluj Moltbot

Jedna komenda instaluje wszystko:

```bash
curl -fsSL https://molt.bot/install.sh | bash
```

Skrypt automatycznie:
- Zainstaluje wymagane zależności
- Skonfiguruje Moltbot
- Uruchomi kreator onboardingu

### 3.2 Konfiguracja

Kreator przeprowadzi Cię przez:
- Konfigurację klucza API (Anthropic/OpenAI)
- Wybór kanałów (Telegram, WhatsApp, Discord, itp.)
- Konfigurację workspace

### 3.3 Sprawdź status

```bash
moltbot doctor
```

---

## Część 4: Przeglądarka (opcjonalnie)

### Brave Browser

```bash
sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg \
  https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] \
  https://brave-browser-apt-release.s3.brave.com/ stable main" | \
  sudo tee /etc/apt/sources.list.d/brave-browser-release.list

sudo apt update
sudo apt install -y brave-browser
```

---

## Bezpieczeństwo

> **Moltbot ma pełny dostęp do systemu.** Nie wystawiaj gateway na publiczny internet bez zabezpieczeń!

### Bezpieczne połączenie VNC przez SSH tunnel

Zamiast otwierać port 5901, użyj tunelu SSH:

```bash
# Na swoim komputerze
ssh -L 5901:localhost:5901 -i klucz.pem ubuntu@TWOJ_IP_SERWERA
```

Następnie w kliencie VNC połącz się z:
```
localhost:5901
```

---

## Przydatne komendy

```bash
# Restart VNC
vncserver -kill :1 && vncserver :1 -geometry 1920x1080 -depth 24

# Status Moltbot
moltbot doctor

# Aktualizacja Moltbot
moltbot update

# Logi Moltbot
journalctl -u moltbot -f
```

---

## Problemy i rozwiązania

| Problem | Rozwiązanie |
|---------|-------------|
| VNC: czarny ekran | Sprawdź czy `~/.vnc/xstartup` ma `chmod +x` |
| VNC: nie można połączyć | Sprawdź czy port 5901 jest otwarty w firewallu |
| Moltbot: brak połączenia | Uruchom `moltbot doctor` i sprawdź konfigurację |
| Moltbot: błąd instalacji | Sprawdź czy masz curl: `sudo apt install curl` |

---

## Pytania?

Masz pytania? Zostaw komentarz pod filmem lub otwórz [Issue](../../issues) w tym repozytorium.

---

## Licencja

MIT
