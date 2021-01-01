upstream nodes {
    server node1;
    server node2;
    /* para incluir mais servers, basta incluir aqui */
}

server {
    lister 80;
    server_name localhost;
    
    location / {
        proxy_pass http://nodes;
        proxy_set_header X-Real-IP $remote_addr;
    }
    
    access_log /path/access.log;
}

- Observer que o protocolo em proxy_pass é necessário porque o <nodes> é substituido por uma entrada existente no upstream.
- proxy_set_header - repassa o IP original de quem chamaou o load balancer.
- Após isso, é necessário ajustar as instancias para usarem o cabecalho X-Real-IP para verificar o IP correto.