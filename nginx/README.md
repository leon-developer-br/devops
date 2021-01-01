*** Introducao
- webserver
- proxy reverso
- caching
- load balancer
- media streaming
- proxy para servico de e-mail

*** Caracteristicas
- extremamente performático
- performa mais qu e o Apache
- consegue suportar um alto volume de conexoes simultaneas
- utiliza pouca memória e suporta alta concorrencia - formato single thread
- suporte a websockets, HTTP2, streaming de media

*** Funcionamento
- Possui um arquivo de configuração que é lido ao iniciar o processo,
- Possui um processo master (Nginx Master Process - single thread). Diferente do Apache, ele não cria novas conexões para cada nova requisição.
- O processo gera workers. São os responsaveis por processar as requisições. 
- Cada worker gera um processo em cada core da CPU. Enquanto um worker esta processando a requisição, o Nginx vai resolver outra requisição de forma assincrona.
- Dessa forma, ele é baseado em eventos / não bloqueante.
- Quando um evento é recebido pelo Master Process, ele segue com o processamento que esta associado a um worker.
- Logo, temos 1 worker por core. 
- No unico processo do worker, ele multiplexa requisicoes para os demais tipos de servicos que podem estar rodando para cada requisicao.

*** Configuração
- Arquivo de configuração -> /etc/nginx/nginx.conf. Raramente deve ser mexido.
- Worker_processes auto -> define a quantidade de workers no numero de cores da máquina / pode ser configurada.
- Para cada worker o numero de conexoes default é 1024;
- Para um dominio, deve ser criado um arquivo em /etc/nginx/conf.d/*.conf.
- Existe um arquivo default de exemplo.

*** Configurando dominio
- listen: porta que vai estar ouvindo
- server_name: nome do dominio
- location: root: caminho da aplicacao | index: arquivo de index

*** Proxy Reverso
- Servico intermediario que recebe as requisioces de diversos clientes, as processa e as encaminha para o serviço correspondente.
- proxy_pass http://node1

*** Load Balancing
- Neste cenário, o Nginx está atuando como Proxy Reverso e vai balancear a carga entre as instancias de um serviço.
- É possivel dar peso a cada instancia do servico caso as instancia possuam hardwares diferentes (cpu / memoria).
- Dessa forma, as requisicoes sao distribuidas de acordo com o peso das máquinas.

*** Health Check
- Passivo: o servidor A não me respondeu, então vou parar de enviar requisicoes para ele momentaneamente. Pode ser definito um tempo de retry.
- Ativo: a cada X tempo, o nginx pode verificar a saude da instancia do servico. Caso a máquina saia do ar, ele tira a instancia do serviço antes de fazer a requisicao. Requer uma licença do Nginx Plus.