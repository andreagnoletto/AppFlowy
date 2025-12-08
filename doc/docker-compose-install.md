# Instalação com Docker Compose

O AppFlowy ainda não possui uma imagem publicada oficialmente, mas você pode gerar e executar a imagem localmente usando o `docker-compose.yml` que já vem no repositório.

## Pré-requisitos
- Servidor Linux com Docker Engine e plugin **docker compose** instalados.
- Acesso ao X11 (necessário porque a imagem atual executa a aplicação desktop). Em um servidor sem interface gráfica, você pode usar um display virtual (ex.: `xvfb`) para fornecer o `DISPLAY`.

## Passo a passo
1. Clone o repositório e entre nele:
   ```bash
   git clone https://github.com/AppFlowy-IO/AppFlowy.git
   cd AppFlowy
   ```
   > Quer buildar **esta** versão específica? Depois de clonar, execute `git checkout <tag-ou-hash>` antes do Compose. O `docker compose ... --build` sempre usa os arquivos presentes no diretório local, então fixar o checkout garante que a imagem reflita exatamente a versão escolhida.
2. Caso esteja em um desktop ou tenha X11 exposto, libere a conexão do Docker com o servidor gráfico (ajuste conforme sua política de segurança):
   ```bash
   xhost local:docker
   ```
3. (Opcional para servidor headless) Suba um display virtual antes de rodar o Compose:
   ```bash
   sudo apt-get update && sudo apt-get install -y xvfb
   Xvfb :99 -screen 0 1280x720x24 &
   export DISPLAY=:99
   ```
4. Construa e inicie o contêiner com o arquivo de Compose existente:
   ```bash
   docker compose -f frontend/scripts/docker-buildfiles/docker-compose.yml up --build
   ```
   - Ele usa o `Dockerfile` em `frontend/scripts/docker-buildfiles/` para gerar a imagem `appflowy/appflowy:latest`.
   - O serviço roda em modo **host network** e monta os sockets do X11. Ajuste os volumes se você preferir outra abordagem (por exemplo, VNC).
5. Para rodar em segundo plano, adicione `-d`:
   ```bash
   docker compose -f frontend/scripts/docker-buildfiles/docker-compose.yml up -d
   ```
6. Para parar/remover o contêiner:
   ```bash
   docker compose -f frontend/scripts/docker-buildfiles/docker-compose.yml down
   ```

> Dica: se você quiser simplificar, copie o arquivo `frontend/scripts/docker-buildfiles/docker-compose.yml` para uma pasta no servidor (por exemplo `/opt/appflowy`) e execute os comandos de Compose a partir dela. Assim o contexto de build fica local e você não precisa manter o repositório inteiro após a imagem ser gerada.
