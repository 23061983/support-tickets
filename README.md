import requests
from bs4 import BeautifulSoup
import re

def scan_website(url):
    try:
        # Normaliza a URL
        if not url.startswith("http"):
            url = "https://" + url
        headers = {
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
        }
        
        # Faz a requisição HTTP
        response = requests.get(url, headers=headers, timeout=10)
        response.raise_for_status()
        
        # Extrai cabeçalhos
        server = response.headers.get("Server", "Não identificado")
        x_powered_by = response.headers.get("X-Powered-By", "Não identificado")
        print(f"[+] Servidor: {server}")
        print(f"[+] X-Powered-By: {x_powered_by}")
        
        # Analisa o conteúdo da página
        soup = BeautifulSoup(response.text, "html.parser")
        
        # Verifica CMS (exemplo: WordPress, Drupal)
        if "wp-content" in response.text:
            print("[+] CMS Detectado: WordPress")
        if "drupal" in response.text.lower():
            print("[+] CMS Detectado: Drupal")
        
        # Verifica frameworks (exemplo: jQuery, Bootstrap)
        scripts = soup.find_all("script")
        for script in scripts:
            src = script.get("src", "")
            if "jquery" in src.lower():
                print("[+] Framework Detectado: jQuery")
            if "bootstrap" in src.lower():
                print("[+] Framework Detectado: Bootstrap")
        
        # Verifica meta tags para geradores
        meta_generator = soup.find("meta", {"name": "generator"})
        if meta_generator:
            print(f"[+] Gerador Detectado: {meta_generator.get('content', 'N/A')}")
        
    except requests.RequestException as e:
        print(f"[!] Erro ao acessar {url}: {e}")