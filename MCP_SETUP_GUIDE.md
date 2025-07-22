# Guide de Configuration des Serveurs MCP pour LibreChat

## Serveurs MCP Pré-configurés

LibreChat est configuré avec deux serveurs MCP par défaut :

### 1. Exa (Recherche Web)
- **Fonction** : Recherche web avancée et extraction d'informations
- **Configuration** : Déjà configuré avec une clé API

### 2. Context7 (Gestion de Contexte)
- **Fonction** : Gestion avancée du contexte et de la mémoire
- **Configuration** : Prêt à l'emploi

## Comment Utiliser les Serveurs MCP

### Dans l'Interface de Chat
1. Sélectionnez l'endpoint "OpenRouter" 
2. **IMPORTANT** : Choisissez un modèle compatible avec les outils :
   - `openai/gpt-4o` (recommandé)
   - `openai/gpt-4o-mini` (économique)
   - `openai/gpt-4-turbo`
   - `anthropic/claude-3.5-sonnet`
   - `anthropic/claude-3-haiku`
   - `google/gemini-pro-1.5`
   - `meta-llama/llama-3.1-70b-instruct`
3. Les serveurs MCP apparaîtront dans un menu déroulant sous votre zone de texte
4. Sélectionnez le serveur MCP que vous souhaitez utiliser

### ⚠️ Modèles NON compatibles
Évitez ces modèles qui ne supportent pas les outils :
- `gpt-3.5-turbo` (ancien modèle sans support d'outils)
- Modèles de base sans capacités de fonction calling

### Avec les Agents
1. Créez ou éditez un agent
2. Cliquez sur "Add Tools" dans le panneau Agent Builder
3. Sélectionnez les serveurs MCP disponibles
4. Activez/désactivez des outils individuels si nécessaire
5. Sauvegardez votre agent

## Ajouter de Nouveaux Serveurs MCP

### Méthode 1 : Utiliser Smithery.ai (Recommandé)

1. **Rechercher des serveurs** : Visitez [smithery.ai](https://smithery.ai)
2. **Sélectionner un serveur** : Cliquez sur le serveur MCP désiré
3. **Configurer pour LibreChat** : 
   - Allez dans l'onglet "Auto" dans la section Connect
   - Sélectionnez "LibreChat" comme client
4. **Installer** : Copiez et exécutez la commande générée dans votre terminal
5. **Redémarrer** : Redémarrez LibreChat avec `docker compose restart`

### Méthode 2 : Configuration Manuelle

Éditez le fichier `librechat.yaml` et ajoutez votre serveur dans la section `mcpServers` :

```yaml
mcpServers:
  # Serveurs existants...
  
  # Nouveau serveur
  mon-nouveau-serveur:
    command: npx
    args:
      - -y
      - "@nom-du-package/serveur-mcp"
    timeout: 60000
    serverInstructions: true
```

### Exemples de Serveurs MCP Populaires

#### Système de Fichiers
```yaml
filesystem:
  command: npx
  args:
    - -y
    - "@modelcontextprotocol/server-filesystem"
    - /chemin/vers/vos/documents
  timeout: 60000
  serverInstructions: true
```

#### Puppeteer (Automatisation Web)
```yaml
puppeteer:
  command: npx
  args:
    - -y
    - "@modelcontextprotocol/server-puppeteer"
  timeout: 60000
  serverInstructions: true
```

#### Serveur API Personnalisé
```yaml
mon-api:
  type: streamable-http
  url: https://api.monentreprise.com/mcp
  headers:
    X-User-ID: "{{LIBRECHAT_USER_ID}}"
    Authorization: "Bearer ${API_TOKEN}"
  timeout: 30000
  serverInstructions: true
```

## Variables d'Environnement Disponibles

Pour les serveurs HTTP, vous pouvez utiliser ces placeholders :

- `{{LIBRECHAT_USER_ID}}` - Identifiant unique de l'utilisateur
- `{{LIBRECHAT_USER_EMAIL}}` - Email de l'utilisateur
- `{{LIBRECHAT_USER_ROLE}}` - Rôle de l'utilisateur (admin, user, etc.)
- `{{LIBRECHAT_USER_USERNAME}}` - Nom d'utilisateur

## Authentification OAuth

Certains serveurs MCP supportent OAuth pour une authentification sécurisée :

```yaml
spotify:
  type: "streamable-http"
  initTimeout: 150000  # Timeout plus élevé pour l'authentification
  url: "https://mcp-spotify-oauth-example.account.workers.dev/mcp"
```

## Dépannage

### Problèmes Courants

1. **Serveur MCP ne démarre pas** : Vérifiez les logs avec `docker logs LibreChat`
2. **Timeout lors de l'initialisation** : Augmentez la valeur `initTimeout`
3. **Outils non disponibles** : Assurez-vous que le modèle sélectionné supporte les outils

### Redémarrage Requis

Après toute modification de la configuration MCP :
```bash
docker compose restart
```

## Sécurité

- Utilisez OAuth quand disponible plutôt que des clés API
- Configurez des timeouts appropriés
- Surveillez l'expiration des tokens dans les logs
- Chaque utilisateur maintient ses propres sessions OAuth

## Support

Pour plus d'informations, consultez :
- [Documentation LibreChat MCP](https://www.librechat.ai/docs/configuration/librechat_yaml/mcp)
- [Smithery.ai](https://smithery.ai) pour découvrir de nouveaux serveurs
- [Spécification MCP](https://modelcontextprotocol.io/) pour comprendre le protocole
