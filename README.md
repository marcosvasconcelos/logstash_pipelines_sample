# logstash_pipelines_sample
Sample multiple pipelines using config files.
# logstash
Configurações do LOGSTASH para minerar e indexar dados entre os datasources e o elasticsearch.

Como configurar:
### Criar a Pipeline de input para o tipo de entrada que é esperada, file, kafka, beats, etc, para maiores informações vide: https://www.elastic.co/guide/en/logstash/current/input-plugins.html
Abaixo segue exemplo de configuração básica e a estrutura do arquivo de configuração.

```
input {
    ##### some input type like (kafka, beats[filebeat, heartbeat, metricbeat], http, elasticsearch, etc) 
    kafka {
        bootstrap_servers => "kafka-server1:9092, kafka-server2:9092"
        topics => ["topico1","topico2","topico3]
        codec => json ## especifica que a entrada é um JSON a ser manipulado
        decorate_events => true ## Habilita capitura dos metadados do kafka
    }
    
}
filter {

     mutate {
        add_field => {
         "topico_evento" => "%{[@metadata][kafka][topic]}" ## Precisa do decorate_events => true 
         ### Sempre colocar a validação de parser e sempre deve ser checado ao final da pieline
         "parser" => "true"
        }
     }

}
output {
        if [parser] == true {
            elasticsearch {
              hosts => "http://elasticsearch_server:9200" ## Nome da URL do Elasticsearch
              index => "eda-%{[topico_evento]}-%{+YYYY.MM.dd}" ## Nome do Indice onde será armazenado os dados filtrados
            }
        } else {
            elasticsearch {
                hosts => "http://elasticsearch_server:9200" ## Nome da URL do Elasticsearch
                index => "error-%{[topico_evento]}-%{+YYYY.MM.dd}" ## Nome do Indice onde será armazenado os dados não filtrados
            }
        } 
}
```
**Outras informações específicas sobre o produto vide: https://www.elastic.co/guide/en/logstash/current/index.html**


**Marcos Vinicius Vasconcelos Silva**
*AIS Digital*
marcos.silva@aisdigital.com.br
