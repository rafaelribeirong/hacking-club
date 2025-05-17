# Obtenção de Dados de Subdomínios: Métodos e Ferramentas

## Diferença entre Forma Ativa e Passiva

**Passiva**: Coleta informações sem enviar tráfego diretamente para o alvo, usando fontes públicas e históricas. É mais discreta e menos intrusiva.

**Ativa**: Envolve interação direta com o alvo através de técnicas como brute force ou varreduras DNS. Pode ser detectada mais facilmente.

## 1. Método Histórico

### SecurityTrails.com
- Plataforma abrangente para pesquisa de DNS e histórico de domínios
- Requer login para acesso completo
- Útil para identificar servidores que já passaram por WAFs em algum momento
- Permite ver alterações históricas em configurações DNS
- Oferece API para automatização de consultas

### SubdomainFinder.c99.nl
- Ferramenta online para enumeração de subdomínios
- Combina várias técnicas de descoberta
- Interface web simples sem necessidade de instalação

### Web.Archive.org (Wayback Machine)
- Arquivo histórico da internet
- Permite ver snapshots antigos de sites e subdomínios
- Pode revelar subdomínios não mais ativos
- Oferece API para automatizar buscas (Wayback Machine API)
- Útil para encontrar informações expostas no passado

### DNSDumpster.com
- Ferramenta gratuita de reconhecimento de DNS
- Gera mapas visuais de infraestrutura
- Identifica subdomínios, servidores DNS e MX records
- Não requer autenticação

### GitHub.com/secretsec/sudomy
- Ferramenta de enumeração de subdomínios
- Combina técnicas passivas e ativas
- Oferece saída formatada em JSON, CSV e HTML
- Pode ser integrada com outras ferramentas

## 2. Método de Brute Force

**Brute Force** é uma técnica ativa que testa combinações possíveis de subdomínios contra um domínio alvo, usando listas de palavras comuns.

### Ferramentas Principais:
- **Sublist3r**: Enumeração rápida usando múltiplas fontes
- **SubBrute**: Ferramenta especializada em brute force DNS
- **Amass**: Ferramenta abrangente que combina técnicas passivas e ativas

**Exemplo no Linux**:
```bash
host subdominio.site.com
```
Podemos baixar listas de subdomínios comuns para usar no brute force, como:
```bash
wget https://gist.githubusercontent.com/.../subdomains.txt
```

## 3. Método de Certificados SSL/TLS (Passivo)

### Ferramentas:
- **SubFinder** (Project Discovery): Ferramenta modular para enumeração
- **CertSpotter** (SSLmate): Monitora certificados em tempo real
- **CTFR**: Usa o crt.sh para listar subdomínios
- **Chaos** (Project Discovery): Dataset público de subdomínios

**Importância dos certificados**:
- Muitas organizações usam certificados wildcard (*.dominio.com)
- Certificados SSL contêm informações valiosas sobre infraestrutura
- A transparência de certificados (CT logs) revela subdomínios
- Pode identificar infraestrutura não listada publicamente

## Instalação e Uso do SubFinder

1. Baixar e instalar:
```bash
wget https://github.com/projectdiscovery/subfinder/releases/...
tar -xzvf subfinder...tar.gz
mv subfinder /opt/tools/
```

2. Configurar chaves API (Shodan, SecurityTrails, etc.) para melhores resultados

3. Uso básico:
```bash
subfinder -d sitealvo.com
```

## CTFR (Usando crt.sh)

O CTFR é uma ferramenta que utiliza o Certificate Transparency logs (especificamente crt.sh) para enumerar subdomínios. Ele:
- Consulta o histórico de certificados SSL
- É totalmente passivo
- Pode revelar subdomínios não listados em outras fontes
- Pode ser integrado com outras ferramentas de enumeração

## Considerações Finais

Para um pentest completo, recomenda-se:
1. Começar com técnicas passivas (SubFinder, SecurityTrails)
2. Complementar com consulta a certificados (CTFR, CertSpotter)
3. Finalizar com brute force (Sublist3r, Amass) para subdomínios não listados
4. Sempre verificar o histórico (Wayback Machine) para informações antigas

Organize suas ferramentas em diretórios específicos (/opt/tools/ ou ~/pentest/) para manter um ambiente de trabalho organizado.