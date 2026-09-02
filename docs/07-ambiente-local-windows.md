# 07 — Estado do Ambiente Local Windows

Atualizado em 2026-09-02.

## Estado confirmado

Usuário Windows local: `cristhyan`.

Ferramentas confirmadas:

- Git `2.55.0.windows.3` — instalado e funcional;
- OpenSSH for Windows `9.5p1` — instalado e funcional;
- GNU Bash `5.3.15` — instalado em `C:\Program Files\Git\bin\bash.exe`;
- PowerShell — terminal atualmente utilizado;
- diretório `C:\Users\cristhyan\.ssh` ainda não existe;
- portanto, nenhuma chave SSH foi criada nesse perfil até este ponto.

## Próximo passo

Criar uma chave SSH Ed25519 no PowerShell:

```powershell
ssh-keygen -t ed25519
```

Aceitar o caminho padrão deverá criar:

```text
C:\Users\cristhyan\.ssh\id_ed25519
C:\Users\cristhyan\.ssh\id_ed25519.pub
```

A chave pública pode ser exibida com:

```powershell
Get-Content $HOME\.ssh\id_ed25519.pub
```

Nunca compartilhar nem versionar `id_ed25519` (chave privada).