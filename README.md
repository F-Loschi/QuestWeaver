# 🎯 RPG Quest System - Fork Especializado

**Fork especializado do RPG Avançado de Quests** - Desenvolvimento focado no sistema de quests, classes de arqueiro e assassino, livro de quests e persistência de dados.

---

## 🚀 Minha Contribuição

Como responsável pela arquitetura e implementação do sistema de quests, desenvolvi:

### 🏗️ **Sistema de Quests Genérico**
- **Arquitetura modular** baseada em herança e polimorfismo
- **Classe abstrata `Quest`** com template methods para comportamento padrão
- **Sistema de eventos** para progresso e conclusão de quests
- **Factory pattern** para criação dinâmica de quests
- **Interface fluente** para construção de quests

### 🏹 **Sistema de Quests do Arqueiro**
- **`RangedCombatQuest`**: Combate a distância com validação de range mínimo
- **`PrecisionHunterQuest`**: Sistema de headshots e tiros críticos  
- **`WindMasterQuest`**: Sistema de combo complexo com timeout e reset progressivo

### 🗡️ **Sistema de Quests do Assassino**
- **`BackstabQuest`**: Detecção vetorial de ataques pelas costas
- **`SpeedKillQuest`**: Sistema de streak com timeout temporal
- **`PerfectKillQuest`**: Execução perfeita sem dano e sem explosões

### 📖 **Sistema de Livro de Quests**
- **Interface interativa** com livro customizado do Minecraft
- **Progresso em tempo real** com barras visuais e estatísticas
- **Atualização dinâmica** sem necessidade de reabrir o livro
- **Histórico completo** de quests concluídas

### 💾 **Sistema de Persistência**
- **Serialização JSON** com GSON para dados de jogadores
- **PersistentDataContainer** para metadata de entidades
- **Sistema de backup** e recovery automático
- **Gestão de estado** complexo (combos, streaks, HP)

---

## 🏹 Quests do Arqueiro (Minha Implementação)

### 📘 **RangedCombatQuest** - Combate a Distância
```java
// Sistema de validação de distância
@Override
protected boolean isValidProjectile(Arrow arrow) {
    double distance = calculateDistance(shooter, arrow);
    return distance >= MIN_DISTANCE && super.isValidProjectile(arrow);
}
```

### 📗 **PrecisionHunterQuest** - Caçador Preciso  
```java
// Detecção de headshots e tiros críticos
public void updateProgress(String mobType, Material weapon, Player player, Arrow arrow) {
    if (isCriticalHit(arrow) && isValidHeadshot(arrow)) {
        incrementProgress(player);
    }
}
```

### 📕 **WindMasterQuest** - Mestre dos Ventos
```java
// Sistema complexo de combo com timeout
private void validateCombo(Player player) {
    long currentTime = System.currentTimeMillis();
    if (currentTime - lastHitTime.get(player.getUniqueId()) > COMBO_TIMEOUT) {
        resetCombo(player, "§c✗ Combo perdido! Timeout excedido.");
    }
}
```

---

## 🗡️ Quests do Assassino (Minha Implementação)

### 🌑 **BackstabQuest** - Sombras Silenciosas
```java
// Detecção vetorial de backstab
private boolean isBackstab(Player player, LivingEntity entity) {
    Vector playerDirection = player.getLocation().getDirection();
    Vector toEntity = entity.getLocation().toVector().subtract(player.getLocation().toVector());
    return playerDirection.dot(toEntity) > BACKSTAB_ANGLE_THRESHOLD;
}
```

### ⚡ **SpeedKillQuest** - Velocidade Mortal
```java
// Sistema de streak com tracking temporal
public void updateProgress(String mobType, Material weapon, Player player) {
    long currentTime = System.currentTimeMillis();
    if (currentTime - lastKillTime > STREAK_TIMEOUT) {
        resetStreak(player);
    }
    incrementStreak(player);
}
```

### 💀 **PerfectKillQuest** - Assassinato Perfeito
```java
// Rastreamento de HP para "zero dano"
private boolean tookDamage(Player player) {
    double currentHealth = player.getHealth();
    boolean damaged = currentHealth < lastRecordedHealth.get(player.getUniqueId());
    lastRecordedHealth.put(player.getUniqueId(), currentHealth);
    return damaged;
}
```

---

## 🏗️ Arquitetura do Sistema

### **Hierarquia de Classes**
```
Quest (Abstract)
├── HitQuest (Abstract)  
│   ├── RangedCombatQuest
│   ├── PrecisionHunterQuest  
│   ├── WindMasterQuest
│   ├── BackstabQuest
│   ├── SpeedKillQuest
│   └── PerfectKillQuest
└── [Future Quest Types]
```

### **Padrões de Design Implementados**
- **Template Method**: Comportamento base em `Quest`
- **Strategy**: Diferentes validações por tipo de quest
- **Observer**: Eventos de progresso e conclusão
- **Factory**: Criação dinâmica de instâncias de quest

### **Sistema de Eventos**
```java
// Evento customizado para conclusão de quests
public class QuestCompletedEvent extends Event {
    private final Player player;
    private final Quest quest;
    
    // Getters e lógica de dispatch
}
```

---

## 📊 Sistema de Persistência

### **Gestão de Dados Complexos**
```java
public class PlayerQuestData {
    private Map<String, Quest> activeQuests;
    private Map<String, Quest> completedQuests;
    private Map<String, Object> questProgress; // Combos, streaks, etc.
}
```

### **Serialização JSON**
```java
// Exemplo de dados persistidos
{
  "playerId": "uuid",
  "activeQuests": {
    "wind_master_quest": {
      "type": "WindMasterQuest",
      "progress": 7,
      "combo": 7,
      "lastHitTime": 1640995200000
    }
  },
  "completedQuests": ["ranged_combat", "precision_hunter"]
}
```

---

## 🧪 Testes Desenvolvidos

### **Testes Unitários Abrangentes**
- ✅ **Testes de inicialização** e configuração de quests
- ✅ **Testes de validação** (distância, headshots, backstabs)
- ✅ **Testes de sistemas complexos** (combo, streak, perfect kill)
- ✅ **Testes de persistência** e serialização
- ✅ **Testes de eventos** e listeners

### **Exemplo de Teste para WindMasterQuest**
```java
@Test
public void testComboSystem() {
    WindMasterQuest quest = new WindMasterQuest(spawnLocation);
    Player player = mock(Player.class);
    
    // Simula acertos consecutivos
    for (int i = 0; i < 5; i++) {
        quest.updateProgress("CREEPER", Material.BOW, player, arrow);
        assertTrue(quest.getCombo(player) > previousCombo);
    }
}
```

---

## 🎯 Features Técnicas Implementadas

### **✅ Sistema de Progresso em Tempo Real**
- Barra de progresso visual no livro de quests
- Mensagens de feedback contextuais
- Atualização sem necessidade de reabrir interfaces

### **✅ Validações Complexas**
- Cálculo vetorial para detecção de backstab
- Sistema de distância euclidiana 3D
- Tracking temporal para combos e streaks
- Monitoramento contínuo de HP para execuções perfeitas

### **✅ Gestão de Estado Avançada**
- Combos com timeout configurável
- Streaks com reset automático
- Progresso persistente entre logins
- Recovery de estado após relog

### **✅ Sistema de Feedback Rico**
- Mensagens coloridas e informativas
- Dicas contextuais de gameplay
- Alertas de falha imediatos
- Confirmações de sucesso destacadas

---

## 🔧 Tecnologias e Ferramentas

- **Java 21** + **Minecraft Spigot API 1.20.4**
- **JUnit 5** + **MockBukkit** para testes
- **GSON** para serialização JSON
- **Kyori Adventure** para componentes de texto
- **Mockito** para mocking em testes

---

## 📈 Métricas do Desenvolvimento

- **~2.500 linhas de código** nas classes de quests
- **~1.500 linhas de testes** unitários
- **6 quests especializadas** implementadas
- **3 sistemas complexos**: combo, streak, perfect execution
- **100% cobertura** dos casos principais de uso

---

## 📞 Contato

**Desenvolvido por [Felipe Silva Loschi]**  
💼 [LinkedIn](https://linkedin.com/in/felipe-silva-loschi)  
🐙 [GitHub](https://github.com/F-Loschi)  
📧 f.s.loschi@gmail.com
