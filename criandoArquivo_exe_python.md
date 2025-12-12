
# 🐍 Guia Completo — Como Criar um Arquivo `.exe` a partir de um Código Python

Este guia apresenta um **passo a passo detalhado** para transformar um script Python (`.py`) em um **executável `.exe` no Windows**, mesmo em máquinas que **não possuem Python instalado**.

Ao final, há um **BÔNUS** explicando como **configurar um ícone personalizado** para o executável.

---

## 📌 Pré-requisitos

- Windows 10 ou superior  
- Python 3.9 ou superior  
  - https://www.python.org/downloads/windows/
  - Marque a opção **Add Python to PATH**
- Prompt de Comando, PowerShell ou Git Bash
- Um script Python funcional (`.py`)

---

## 📁 Estrutura Recomendada do Projeto

```
meu_projeto/
│
├─ main.py
├─ requirements.txt   (opcional)
└─ icon.ico           (opcional – bônus)
```

---

## 1️⃣ Criar um Ambiente Virtual (Recomendado)

```
python -m venv venv
venv\Scripts\activate
```

---

## 2️⃣ Instalar Dependências

```
pip install -r requirements.txt
```

Ou manualmente:

```
pip install pandas requests openpyxl
```

---

## 3️⃣ Instalar o PyInstaller

```
pip install pyinstaller
pyinstaller --version
```

---

## 4️⃣ Gerar o Executável

Executável único:

```
pyinstaller --onefile main.py
```

Sem console:

```
pyinstaller --onefile --noconsole main.py
```

---

## 5️⃣ Testar o Executável

- Abra a pasta `dist`
- Execute o `.exe`
- Teste fora da pasta do projeto

---

## 🧹 Limpar Builds Antigos

```
rmdir /s /q build
rmdir /s /q dist
del main.spec
```

---

# 🎁 BÔNUS — Ícone Personalizado

## Criar ícone `.ico`

Sites recomendados:
- https://www.icoconverter.com/
- https://convertico.com/

Salvar como `icon.ico`

---

## Gerar o `.exe` com ícone

```
pyinstaller --onefile --noconsole --icon=icon.ico --name MeuAplicativo main.py
```

---

## ✅ Conclusão

Você aprendeu a:

- Criar `.exe` a partir de Python
- Gerar executável único
- Configurar ícone personalizado
- Distribuir sua aplicação com segurança
