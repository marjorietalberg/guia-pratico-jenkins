FROM node:21.6.0-alpine3.18

# Diretório de trabalho dentro do container
WORKDIR /app

# Copia os arquivos de dependências
COPY package*.json ./

# Instala as dependências
RUN npm install

# Copia o restante dos arquivos do projeto
COPY . .

# Expondo a porta 9090 (documentação, não faz o bind sozinho)
EXPOSE 9090

# Comando para rodar o servidor
CMD ["node", "server.js"]
