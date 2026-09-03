# 08 — Troubleshooting Docker + WSL no Windows

## Estado diagnosticado em 2026-09-03

No computador Windows de teste local:

- CPU: AMD Ryzen 5 3400G with Radeon Vega Graphics;
- `VirtualizationFirmwareEnabled = True`;
- `VMMonitorModeExtensions = True`;
- `SecondLevelAddressTranslationExtensions = True`;
- Docker Desktop instalado;
- Docker CLI e Compose instalados;
- WSL atualizado para `2.7.12.0`;
- kernel WSL `6.18.33.2-2`;
- Windows 10 build `19045.6466`;
- `wsl --status` informa versão padrão 2;
- `hypervisorlaunchtype = Auto`;
- componentes `Microsoft-Windows-Subsystem-Linux` e `VirtualMachinePlatform` foram habilitados com sucesso e o Windows foi reiniciado;
- nenhuma distribuição WSL de usuário está instalada;
- serviço `com.docker.service` aparece `Stopped`;
- Docker Desktop não inicia o Linux Engine.

## Erro atual confirmado nos logs do Docker

O comando:

```powershell
docker desktop logs -p 2 | Select-Object -Last 80
```

mostrou que o Docker tenta registrar/importar a distribuição interna `docker-desktop`, mas a criação da VM falha com:

```text
Wsl/Service/RegisterDistro/CreateVm/HCS/HCS_E_HYPERV_NOT_INSTALLED
```

A mensagem do WSL informa que o WSL2 não pode ser iniciado porque a virtualização/hipervisor não está efetivamente disponível para criação da VM.

Também aparecem erros anteriores de `docker-desktop` não encontrado, o que é consequência de a distribuição interna ainda não ter sido criada.

## Interpretação atual

A ausência de uma distribuição Linux de usuário não é, por si só, a causa principal: o Docker Desktop usa/cria sua própria distribuição interna. O ponto de falha atual é a camada de virtualização utilizada pelo WSL 2/HCS ao tentar criar a VM.

Embora a CPU informe virtualização de firmware ativa e `hypervisorlaunchtype` esteja em `Auto`, ainda é necessário confirmar se o hipervisor foi realmente carregado pelo Windows e se os componentes/serviços de virtualização estão ativos no sistema em execução.

## Próximo diagnóstico

No PowerShell como Administrador, verificar:

```powershell
systeminfo | findstr /i "Hyper-V"
Get-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
Get-Service vmcompute,HvHost -ErrorAction SilentlyContinue
```

O `systeminfo` deve indicar que um hipervisor foi detectado. Se não indicar, o problema ainda está na carga/instalação efetiva do hipervisor, apesar de `bcdedit` estar configurado como `Auto`.

Não voltar ao instalador do Vibestack até `docker info` responder com uma seção `Server` funcional.
