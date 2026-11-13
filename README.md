DevOps Python API Descrição da Aplicação e Objetivo Esta aplicação é uma API desenvolvida em Python. O objetivo deste projeto é demonstrar a implementação de um ciclo de vida de desenvolvimento de software (SDLC) completo com práticas de DevOps. O repositório conta com integração contínua (CI) para garantir a qualidade do código e entrega contínua (CD) para automatizar a publicação na nuvem (AWS), eliminando intervenções manuais na implantação.

Tecnologias Utilizadas Linguagem: Python 3.x

Qualidade e Testes: Ruff (Lint e Formatação), Pytest (Testes unitários e cobertura)

Containerização: Docker

CI/CD: GitHub Actions

Cloud Provider (AWS): Amazon ECR (Elastic Container Registry) para armazenamento de imagens e Amazon EC2 (Elastic Compute Cloud) para hospedagem da aplicação.

Instruções para Execução Local Clone o repositório: git clone https://github.com/Da20-Costa/devops-python-api.git cd devops-python-api

Crie e ative um ambiente virtual: python -m venv venv source venv/bin/activate

Instale as dependências: pip install -r requirements.txt pip install -r requirements-dev.txt

Execute a aplicação: uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload

Procedimento de Construção e Execução (Docker) Construa a imagem Docker: docker build -t devops-python-api:local .

Execute o contêiner: docker run -d --name devops-python-api -p 8000:8000 devops-python-api:local

Verifique os logs se necessário: docker logs devops-python-api

Pipelines de Integração e Entrega Contínua (CI/CD) O projeto utiliza o GitHub Actions para orquestrar as pipelines, divididas em etapas lógicas:

Integração Contínua (CI) Executada a cada Push ou Pull Request para a branch main.

Qualidade de Código: Instala dependências, executa o ruff para verificar formatação e falhas estáticas.

Testes Automatizados: Executa o pytest para rodar a suíte de testes e gerar relatórios de cobertura em XML.

Smoke Test em Container: Constrói a imagem Docker, sobe o contêiner e realiza requisições HTTP na rota /health para garantir que a aplicação inicializa corretamente antes do deploy.

Entrega Contínua (CD) Executada apenas após um Push (merge) bem-sucedido na branch main e aprovação na etapa de CI.

Push para o ECR: Autentica na AWS usando OIDC/Secrets, constrói a imagem com a tag correspondente ao commit da versão (github.sha) e envia para o Amazon ECR.

Deploy na EC2: Estabelece uma conexão SSH segura com a instância EC2.

Atualização do Contêiner: A instância autentica no ECR, baixa a nova imagem, para e remove o contêiner da versão antiga, e inicia a nova versão mapeando a porta 8000.

Arquitetura de Implantação A infraestrutura foi desenhada da seguinte forma:

O desenvolvedor realiza o push do código no GitHub.

O GitHub Actions assume o processo, realizando o build e enviando a imagem Docker para um repositório privado no Amazon ECR.

Via protocolo SSH, o GitHub Actions acessa a instância Amazon EC2.

A instância EC2 possui uma IAM Role anexada que a autoriza a baixar a imagem do ECR (permissão ReadOnly) sem a necessidade de chaves fixas no servidor.

O contêiner é substituído e passa a expor a aplicação na porta 8000.

Acesso à API Publicada A aplicação está disponível e acessível publicamente através do endereço:

URL Base: http://3.90.235.203:8000

Health Check: http://3.90.235.203:8000/health

Instruções para Verificar a Versão Implantada Para validar qual versão da aplicação está rodando atualmente em produção:

Acesse a rota de Health Check ou a rota raiz da API no navegador.

Identifique a tag do commit do último merge na branch main do repositório.

Se desejar verificar internamente na EC2, conecte-se via SSH e execute: docker ps A coluna IMAGE mostrará a URI do ECR terminada com o hash do último commit.

Evidências da Execução das Pipelines (Adicione os prints de tela que comprovem o funcionamento do fluxo nos caminhos abaixo)

Pipeline de CI/CD finalizada com sucesso (GitHub Actions): ./docs/ci-cd-success.png

Imagem armazenada no Amazon ECR: ./docs/ecr-image.png

Aplicação respondendo na EC2 (Browser/Postman): ./docs/api-response.png

Nota de Segurança: Por boas práticas, nenhuma credencial, token ou chave primária foi embutida neste código-fonte ou documentação. Todos os segredos operacionais estão isolados utilizando o GitHub Actions Secrets e AWS IAM Roles.
