# 💬 Chat de Suporte Brainsoft

![.NET](https://img.shields.io/badge/.NET-8.0-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23-12-239120?style=for-the-badge&logo=c-sharp&logoColor=white)
![Windows Forms](https://img.shields.io/badge/Windows%20Forms-Blue?style=for-the-badge&logo=windows&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![Inno Setup](https://img.shields.io/badge/Inno_Setup-6.2-lightgrey?style=for-the-badge)

Sistema desktop desenvolvido em **C# .NET 8 (Windows Forms)** para facilitar e agilizar a comunicação direta entre os usuários e a equipe de suporte da **Brainsoft**. A aplicação integra o widget do Tawk.to nativamente no Windows através do WebView2.

## ✨ Principais Funcionalidades

* **Integração Nativa com Tawk.to:** Utiliza a biblioteca **WebView2** rodando sobre um servidor HTTP local embutido para renderizar o chat de suporte de forma rápida e fluida.
* **Contexto Inteligente (Banco IMOB):** Ao iniciar, a aplicação tenta estabelecer conexão com o banco de dados **PostgreSQL** do sistema IMOB. 
  * ✅ *Se bem-sucedido:* Coleta informações relevantes do cliente e as injeta no Tawk.to, municiando o atendente com dados valiosos para um suporte ágil.
  * ⚠️ *Se falhar:* O sistema continua funcionando perfeitamente, permitindo a abertura do chat sem os dados de contexto.
* **Arquitetura Adaptável (32/64 bits):** O instalador inteligente identifica a arquitetura do sistema operacional do cliente e instala a versão compilada adequada (x86 ou x64).
* **Atualização Automática (OTA):** Sistema autônomo de verificação de atualizações (via Vercel e GitHub Releases), garantindo que o cliente sempre tenha a versão mais recente do chat sem intervenção manual.

---

## 🏗️ Estrutura da Solução

Para evitar travamento de arquivos (*File Lock*) durante atualizações, a solução é dividida em dois executáveis:

1. **`Brainsoft.Connect` (Principal):** Interface de usuário, servidor local, motor WebView2 e conexão PostgreSQL.
2. **`Brainsoft.Updater` (Atualizador):** Executável secundário que encerra o app principal, extrai pacotes de atualização em segundo plano e reinicia o sistema.

---

## ⚙️ Pré-requisitos e Tecnologias

* [Visual Studio 2022](https://visualstudio.microsoft.com/pt-br/vs/)
* [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
* Pacote NuGet: `Microsoft.Web.WebView2`
* Pacote NuGet: `Npgsql` (Driver PostgreSQL)
* [Inno Setup 6](https://jrsoftware.org/isdl.php) (Para compilar o instalador inteligente)

---

## 🚀 Como Compilar e Publicar

### 1. Compilação (x86 e x64)
No Visual Studio, você precisará gerar duas publicações *Single File* distintas para atender a todos os clientes:
* Perfil 1: Target Runtime `win-x86` (Para sistemas 32-bits antigos).
* Perfil 2: Target Runtime `win-x64` (Para sistemas 64-bits modernos).

### 2. Geração do Instalador (Inno Setup)
O script do Inno Setup (`Setup.iss`) está configurado com a flag `ArchitecturesInstallIn64BitMode=x64`. Ele carrega os binários gerados no passo anterior e decide em tempo de instalação qual pasta e qual executável utilizar, otimizando o desempenho na máquina do cliente.

### 3. Publicar uma Atualização
1. Altere a versão da aplicação no `.csproj`.
2. Publique os novos executáveis.
3. Compacte os arquivos no `update.zip` e publique no **GitHub Releases**.
4. Atualize o arquivo `update.json` hospedado na **Vercel** para notificar os clientes ativos.

---

## 📁 Arquitetura de Configuração Local

O sistema salva configurações vitais na pasta de dados do usuário (`AppData`), garantindo que não existam problemas de permissão de administrador na leitura/escrita.

* **Caminho:** `%AppData%\Brainsoft Connect\BrainsoftConnect_v?.?.?.json`
* **Exemplo de Conteúdo:**
```json
{
  "Termo": {
    "termo_aceite": true,
    "data": "2026-05-02T15:23:53.2843624-03:00"
  },
  "CaminhoPastaImob": "C:\\IMOB",
  "ModoAtualizacao": 2,
  "UltimaVerificacao": "0001-01-01T00:00:00"
}
