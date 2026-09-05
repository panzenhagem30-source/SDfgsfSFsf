# POC Android Farm com WireGuard por dispositivo (location-based)

Este repositório contém um Proof-of-Concept (POC) para uma "fazenda" de emuladores Android que permite rotear cada dispositivo virtual por um túnel WireGuard para uma localização/região específica. O objetivo é permitir que cada "telefone" tenha tráfego de saída com IP geolocalizado conforme o exit node WireGuard configurado.

AVISO: este POC é para fins de desenvolvimento/testes. Não exponha ADB ou containers sem proteção adequada em produção. Respeite termos de serviço e leis locais ao enviar automações, SMS ou uso de IPs.

Conteúdo do POC
- docker-compose.yml: orchestrador para levantar 1 emulador Android, backend FastAPI, worker e Redis.
- android/: Dockerfile e scripts para construir o container Android com WireGuard.
- backend/: FastAPI simples com endpoints para listar dispositivos e enfileirar jobs.
- worker/: executor simples que consome Redis e executa comandos ADB (tap, text, screencap).
- artifacts/: pasta para screenshots e artefatos produzidos pelos jobs.

Pré-requisitos
- Docker e docker-compose instalados no host.
- Kernel com suporte a WireGuard (ou executar WireGuard no host e compartilhar network namespace).
- adb disponível dentro do container android (a imagem base já inclui adb). Para usar ADB local, instale adb no host.

Passos rápidos (POC)

1) Preparar exit nodes WireGuard
 - Crie VMs/Servers nas regiões desejadas (ex.: Brazil, US, DE) e instale WireGuard server em cada uma.
 - Gere pares de chaves para cada peer (cada device/container terá sua pair).
 - Para cada device/container crie um arquivo wg0.conf que aponte para o servidor WireGuard escolhido (peer private key, AllowedIPs = 0.0.0.0/0, Endpoint ip:port).

2) Coloque o wg0.conf para o container android em `./android/wg/wg0.conf`

3) Build & Up

```bash
docker-compose up --build
```

4) Teste
 - Acesse backend: http://localhost:8000
 - Criar job (exemplo):
   POST /jobs
   {
     "device": "127.0.0.1:5555",
     "actions": [{"type":"screencap","name":"t1.png"}]
   }
 - Verifique `artifacts/` para screenshots.

Arquivos principais (descrição rápida)

- docker-compose.yml: define os serviços `redis`, `backend`, `worker`, `android_emulator`.
- android/Dockerfile: imagem baseada em um emulador Android pública, instala wireguard-tools e copia o `entrypoint.sh`.
- android/entrypoint.sh: ativa o wg-quick se `wg0.conf` existir e depois inicia o emulador (ajuste conforme a imagem base).
- backend/app/main.py: FastAPI mínimo com endpoints `/devices`, `/jobs` e `/health`.
- worker/worker.py: consumidor Redis que executa comandos ADB para ações: tap, text, screencap.
- README.md: este arquivo com instruções rápidas.

Notas importantes e limitações
- O uso de WireGuard dentro de containers requer privilégios (NET_ADMIN) e suporte no kernel do host. Alternativa: executar WireGuard no host e compartilhar o namespace de rede do host com o container.
- Emuladores Android em container podem requerer KVM/GPU e ajustes dependendo do host. Nem todas as imagens públicas funcionam sem configuração adicional.
- IPs obtidos via VPS datacenter serão geolocalizados para o datacenter. Se precisar de IPs residenciais ou de operadora móvel, considere provedores de proxies residenciais ou gateways SIM (custo e legalidade a considerar).
- Segurança: não exponha ADB na internet sem restrições, use redes privadas e firewalls.

Próximos passos que posso automatizar para você
- Ajustar docker-compose para múltiplos dispositivos (naming dinâmico e portas ADB mapeadas).
- Gerar scripts para criar pares WireGuard (server/client) e facilitar a criação de wg0.conf para cada device.
- Adicionar PostgreSQL para persistência, autenticação e frontend React.
- Criar repositório, commitar todo o scaffold e abrir PR com instruções CI/CD e deploy em Kubernetes.

Se quiser que eu comite este scaffold neste repositório agora, responda confirmando a mensagem de commit desejada. Por padrão usarei: "POC: add android-farm scaffold README".
