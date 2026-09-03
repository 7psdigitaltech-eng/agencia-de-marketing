# 08 — Troubleshooting Docker + WSL no Windows

## Estado diagnosticado em 2026-09-03

No computador Windows de teste local:

- CPU: AMD Ryzen 5 3400G with Radeon Vega Graphics;
- `VirtualizationFirmwareEnabled = True`;
- `VMMonitorModeExtensions = True`;
- `SecondLevelAddressTranslationExtensions = True`;
- Docker Desktop instalado;
- Docker CLI e Compose instalados;
- Docker Engine não inicia;
- Docker Desktop exibe `Virtualization support not detected`;
- `wsl --version` não retorna versão e mostra apenas a ajuda do comando.

## Interpretação

A virtualização de hardware está habilitada no firmware. O sintoma de `wsl --version` sem informações de versão é compatível com a versão inbox/legada do WSL, que não atende os requisitos atuais do Docker Desktop.

O Docker Desktop atual requer WSL 2.1.5 ou superior e recomenda manter o WSL atualizado.

## Próxima correção recomendada

No PowerShell como Administrador:

```powershell
wsl --install --no-distribution --web-download
```

Esse comando deve instalar/atualizar a infraestrutura moderna do WSL sem necessidade de instalar uma distribuição Linux de usuário, já que o Docker Desktop utiliza seu próprio backend WSL.

Se o comando não conseguir concluir a configuração, habilitar explicitamente os componentes do Windows:

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
bcdedit /set hypervisorlaunchtype auto
```

Depois reiniciar o Windows.

Após o reboot, validar:

```powershell
wsl --version
wsl --status
wsl -l -v
```

Então abrir o Docker Desktop e validar:

```powershell
docker info
```

Não voltar ao instalador do Vibestack até `docker info` responder com uma seção `Server` funcional.
