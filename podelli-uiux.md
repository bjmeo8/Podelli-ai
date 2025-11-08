# Podelli - Design UX/UI des Actions du Feed

## 📋 Table des Matières

1. [Structure Globale Commune](#structure-globale-commune)
2. [Action 1 - Vidéo Interactive (Video Quiz)](#action-1---vidéo-interactive-video-quiz)
3. [Action 2 - Voice Spell](#action-2---voice-spell)
4. [Action 3 - Quiz (Text / Image / Audio)](#action-3---quiz-text--image--audio)
5. [Action 4 - Speaking Task](#action-4---speaking-task)
6. [Transitions entre Actions](#transitions-entre-actions)

---

## Structure Globale Commune

Toutes les actions du feed partagent une structure commune pour maintenir la cohérence de l'expérience utilisateur.

### 📐 Dimensions du Container

```
Hauteur: calc(100vh - 73px - 66px)
  • 73px = hauteur du header fixe
  • 66px = hauteur du bottom nav
Largeur: 100vw (max-width: 512px centré)
Position: relative
Scroll-snap: start
```

### 🎯 Header (Top Overlay)

**Position**: Absolute, top: 0, left: 0, right: 0  
**Gradient Background**: `linear-gradient(180deg, rgba(0,0,0,0.6) 0%, rgba(0,0,0,0) 100%)`  
**Padding**: 24px (p-6)  
**Z-index**: 10

#### Contenu du Header

```
┌─────────────────────────────────────────┐
│  [Mission N]        [Type d'Action]     │
└─────────────────────────────────────────┘
```

**Tag Gauche - Mission**:
- Texte: "Mission N" (où N = numéro de la mission, ex: "Mission 1", "Mission 2", etc.)
- Background: `rgba(255, 255, 255, 0.2)` + `backdrop-blur-sm`
- Padding: `px-3 py-1.5` (12px horizontal, 6px vertical)
- Border-radius: `rounded-full`
- Font: 14px (text-sm), Semi-Bold (600)
- Color: white

**Tag Droit - Type d'Action**:
- Texte: "VIDEO QUIZ" | "VOICE SPELL" | "QUIZ" | "SPEAKING"
- Background: `hsl(164, 86%, 45%)` avec 80% opacity + `backdrop-blur-sm`
- Padding: `px-3 py-1.5`
- Border-radius: `rounded-full`
- Font: 12px (text-xs), Bold (700)
- Color: white
- Text-transform: UPPERCASE

### 🎯 Footer (Bottom Overlay)

**Position**: Absolute, bottom: 0, left: 0, right: 0  
**Gradient Background**: `linear-gradient(0deg, rgba(0,0,0,0.8) 0%, rgba(0,0,0,0) 100%)`  
**Padding**: 24px (p-6)  
**Padding-bottom**: 16px (pb-4)
**Z-index**: 10

#### Contenu du Footer

```
┌─────────────────────────────────────────┐
│  Épisode 1 - Contrôle des passeports    │
│  Apprendre à se présenter et à          │
│  répondre aux questions de la douane    │
└─────────────────────────────────────────┘
```

**Titre de l'épisode**:
- Format: "Épisode [N] - [Titre]"
- Font: 24px (text-2xl), Bold (700)
- Color: white
- Margin-bottom: 8px (mb-2)

**Description de l'épisode**:
- Texte: Résumé ou objectif de l'épisode
- Font: 14px (text-sm), Regular (400)
- Color: `rgba(255, 255, 255, 0.8)`
- Line-height: 1.4
- Max-lines: 2 (text-clamp)

---

## Action 1 - Vidéo Interactive (Video Quiz)

### 📺 Structure Visuelle

```
┌────────────────────────────────────────────┐
│ [Mission N]              [VIDEO QUIZ]      │ ← Header Overlay
│                                            │
│                                            │
│            [Vidéo en lecture]              │
│                                            │
│                                            │
│         [Tap pour révéler]                 │ ← Indicateur visuel
│                  ↓                         │
│                                            │
│                                            │
│ Épisode 1 - Contrôle des passeports       │ ← Footer Overlay
│ Apprendre à se présenter à la douane      │
└────────────────────────────────────────────┘
```

### 🎬 Vidéo Background

**Element**: `<video>` en autoplay loop (avec son)  
**Position**: Absolute, inset: 0  
**Object-fit**: cover  
**Z-index**: 1  

**Comportements**:
- Lecture automatique au chargement
- Tap 1 fois → Pause (affiche icône pause centrale)
- Tap à nouveau → Reprend la lecture
- Pas de double-tap (pas de like)

### 🎯 Déclencheur des Questions

**Solution Optimale: Déclencheur Automatique à la Fin de la Vidéo**

Lorsque la vidéo arrive à sa fin (dernière seconde), les questions apparaissent automatiquement avec une animation fluide. Cela permet de:
- Ne pas obstruer la vidéo pendant la lecture
- Laisser l'utilisateur se concentrer sur le contenu d'abord
- Créer un flow naturel: regarder → répondre

**Animation d'Apparition**:
```
Timeline:
1. Vidéo joue normalement (0-6s)
2. À 6.5s: overlay semi-transparent fade in (300ms)
3. À 6.8s: card questions slide up depuis le bas (400ms ease-out)
4. Vidéo se met en pause automatiquement
5. Vidéo continue de jouer en arrière-plan avec opacity: 0.4
```

**Indicateur Visuel (pendant la vidéo)**:
```css
Position: Bottom center (au-dessus du footer)
Bottom: 180px
Display: Small chip avec animation pulse

Contenu:
  ┌──────────────────┐
  │  ↑ Question      │
  └──────────────────┘

Style:
  - Background: rgba(255, 255, 255, 0.9) + backdrop-blur
  - Padding: 8px 16px
  - Border-radius: 20px (rounded-full)
  - Font: 12px, Semi-Bold
  - Color: #1F2937
  - Animation: translateY bounce (±5px, 2s infinite)
  - Opacity pulse: 0.7 ↔ 1 (2s loop)
```

### ❓ Questions Overlay (Style Story Instagram)

**Overlay Background**:
```css
Position: Absolute, inset: 0
Background: rgba(0, 0, 0, 0.6) + backdrop-blur-sm
Z-index: 15
Animation entrée: fade-in 300ms
Permet de voir la vidéo en arrière-plan (paused, opacity: 0.4)
```

**Container Questions**:
- Position: Absolute, centrée verticalement et horizontalement
- Bottom: 160px (pour ne pas chevaucher le footer)
- Width: calc(100% - 48px) (marge 24px de chaque côté)
- Max-width: 400px
- Z-index: 20
- Background: `rgba(255, 255, 255, 0.95)` + `backdrop-blur-lg`
- Padding: 24px (p-6)
- Border-radius: 24px (rounded-3xl)
- Border: `1px solid rgba(255, 255, 255, 0.2)`
- Box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3)
- Animation: slide-up + fade-in (400ms ease-out)

**Intitulé de la Question** (remplace "Questions overlay"):
```
Exemple: "Que dit l'agent ?"
ou "Quelle est la bonne traduction ?"
```

- Font: 18px (text-lg), Bold (700)
- Color: #1F2937 (gris très foncé, pas blanc)
- Margin-bottom: 20px (mb-5)
- Text-align: center
- Line-height: 1.3

**Options de Réponse**:
- Display: Flex column
- Gap: 12px (gap-3)

**Chaque Option**:
```css
État Normal:
  - Background: white
  - Border: 2px solid #E5E7EB
  - Border-radius: 16px (rounded-2xl)
  - Padding: 16px (p-4)
  - Font: 15px, Medium (500)
  - Color: #1F2937
  - Cursor: pointer
  - Transition: all 0.2s ease
  - Box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05)
  
État Hover:
  - Border: 2px solid #1CB79A
  - Transform: translateY(-2px)
  - Box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1)
  
État Sélectionné (avant validation):
  - Background: rgba(28, 183, 154, 0.1)
  - Border: 3px solid rgb(28, 183, 154)
  - Transform: scale(1.02)
```

**Bouton de Validation**:
- Position: En dessous des options
- Margin-top: 20px (mt-5)
- Width: 100%
- Height: 52px (h-13)
- Background: `hsl(164, 86%, 45%)` (Accent)
- Border-radius: 26px (rounded-full)
- Font: 16px, Bold (700)
- Color: white
- Text: "Valider"
- Disabled si aucune sélection:
  - Opacity: 0.5
  - Cursor: not-allowed
  - Background: #CBD5E1

### ✅ Feedback Visuel (Après Validation)

**Réponse Correcte**:
```
Animation:
  1. Border devient verte: 3px solid #22C55E
  2. Background: rgba(34, 197, 94, 0.15)
  3. Icône ✓ apparaît à droite (28px, verte, scale-in)
  4. Animation confetti (optionnelle, 30 particules légères)
  5. Son "ding" positif (200ms)
  6. Vibration haptique courte (50ms)
  
Durée: 1.2 secondes puis transition
```

**Réponse Incorrecte**:
```
Animation:
  1. Réponse sélectionnée:
     - Border rouge: 3px solid #EF4444
     - Background: rgba(239, 68, 68, 0.15)
     - Icône ✕ à droite (28px, rouge)
     - Shake animation (translateX ±8px, 3 fois, 400ms)
  
  2. Bonne réponse révélée:
     - Border verte: 3px solid #22C55E
     - Background: rgba(34, 197, 94, 0.15)
     - Icône ✓ à droite
     - Highlight glow animation (pulse 600ms)
  
  3. Autres réponses:
     - Opacity: 0.4
     - Pointer-events: none
  
  4. Son "buzz" négatif (150ms)
  5. Vibration haptique double (100ms pause 50ms 100ms)
  
Durée: 1.8 secondes puis transition
```

**Transition vers Action Suivante**:
```
Délai: 1.2-1.8s selon feedback (correct/incorrect)
Animation: 
  1. Questions card slide-down + fade-out (400ms)
  2. Overlay fade-out (300ms)
  3. Scroll snap automatique vers action suivante
```

---

## Action 2 - Voice Spell

### 🎬 Structure Visuelle

```
┌────────────────────────────────────────────┐
│ [Mission N]              [VOICE SPELL]     │ ← Header
│                                            │
│                                            │
│        [Vidéo avec scène statique]         │
│        [+ Caption synchronisée]            │
│                                            │
│         "Bonjour monsieur"                 │ ← Caption
│                                            │
│                                            │
│    ┌───────────────────────────────┐      │
│    │  Bonjour    │    monsieur     │      │ ← Blocs de mots
│    │  /bohn-zhoor│  /muh-syuh/     │      │
│    └───────────────────────────────┘      │
│                                            │
│ Épisode 1 - Contrôle des passeports       │ ← Footer
│ Maîtriser la prononciation correcte       │
└────────────────────────────────────────────┘
```

### 🎥 Vidéo avec Scène Statique

**Element**: `<video>` avec image fixe en background  
**Durée**: 5-12 secondes  
**Contenu**: 
- Background: Image statique contextualisée (ex: passeport, aéroport, bureau)
- Audio: Voix native prononçant la phrase
- Caption: Texte synchronisé avec l'audio

**Style de la Vidéo**:
```css
Position: Absolute, inset: 0
Object-fit: cover
Z-index: 1

Background-image: 
  - Filtre: brightness(0.7) pour assurer contraste du texte
  - Overlay gradient subtil (optionnel):
    linear-gradient(180deg, 
      rgba(0,0,0,0.3) 0%, 
      transparent 30%, 
      transparent 70%, 
      rgba(0,0,0,0.4) 100%
    )
```

**Comportement Interactif**:
```
État Initial: Lecture automatique
Tap écran → Pause + icône pause apparaît
Tap à nouveau → Reprend lecture
```

**Icône Pause/Play Centrale**:
```css
Apparition en Pause:
  - Size: 80x80px
  - Background: rgba(255, 255, 255, 0.3) + backdrop-blur-sm
  - Border-radius: 50%
  - Border: 3px solid white
  - Icône: Pause ⏸ ou Play ▶ (40x40px)
  - Animation: fade-in 200ms + scale(0.9 → 1)
  - Position: center absolu
  - Z-index: 25
```

### 📝 Caption Synchronisée (Style Netflix/YouTube)

**Container Caption**:
```css
Position: Absolute
Bottom: 50% (milieu de l'écran, au-dessus des blocs phonétiques)
Left: 50%
Transform: translateX(-50%)
Width: calc(100% - 64px)
Max-width: 450px
Z-index: 20
Padding: 16px 24px (px-6 py-4)
Background: rgba(0, 0, 0, 0.85) + backdrop-blur-md
Border-radius: 12px (rounded-xl)
```

**Texte Caption**:
```css
Font: 20px (text-xl), Bold (700)
Color: white
Text-align: center
Line-height: 1.4
Letter-spacing: 0.3px
Text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5)

Animation (au rythme de la voix):
  - Mot par mot highlight (fond jaune semi-transparent)
  - Ou fade-in progressif par mot
  - Synchronisation avec timestamps audio
```

**Exemple d'Animation de Mots**:
```css
@keyframes highlight-word {
  0% { 
    background: transparent;
  }
  10% {
    background: rgba(251, 191, 36, 0.4);
  }
  90% {
    background: rgba(251, 191, 36, 0.4);
  }
  100% {
    background: transparent;
  }
}

.word.active {
  animation: highlight-word 0.8s ease;
  transition: all 0.3s;
}
```

### 🔤 Blocs Texte + Phonétique (Après Milieu d'Écran)

**Container Principal**:
```css
Position: Absolute
Top: 55% (après la moitié + marge pour caption)
Left: 0
Right: 0
Padding: 0 24px (px-6)
Z-index: 20
Display: Flex
Flex-direction: column
Gap: 12px (gap-3)
```

**Layout des Blocs**:
```
┌─────────────────────────────────────────┐
│  ┌──────────┐  ┌──────────┐            │
│  │ Bonjour  │  │ monsieur │            │
│  │ /bohn-   │  │ /muh-    │            │
│  │  zhoor/  │  │  syuh/   │            │
│  └──────────┘  └──────────┘            │
│                                         │
│  ┌──────────────┐  ┌─────────────┐    │
│  │ votre        │  │ passeport   │    │
│  │ /vo-truh/    │  │ /pahs-por/  │    │
│  └──────────────┘  └─────────────┘    │
└─────────────────────────────────────────┘
```

**Chaque Bloc (Mot + Phonétique)**:
```css
Container:
  - Display: Inline-flex (pour s'aligner horizontalement)
  - Flex-direction: column
  - Background: rgba(255, 255, 255, 0.95) + backdrop-blur-md
  - Border: 2px solid rgba(255, 255, 255, 0.5)
  - Border-radius: 16px (rounded-2xl)
  - Padding: 16px 20px (px-5 py-4)
  - Margin: 0 6px 12px (gap horizontal et vertical)
  - Box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15)
  - Min-width: fit-content
  - Animation: slide-up + fade-in (stagger 100ms par bloc)

Texte Normal (Mot):
  - Font: 19px (text-lg), Bold (700)
  - Color: #1F2937
  - Text-align: center
  - Margin-bottom: 8px (mb-2)
  - Letter-spacing: 0.5px

Séparateur:
  - Height: 2px
  - Background: linear-gradient(90deg, 
      transparent, 
      rgba(28, 183, 154, 0.5), 
      transparent)
  - Margin: 8px 0
  - Width: 100%

Texte Phonétique:
  - Font: 16px, Regular (400), Mono (Courier New)
  - Color: hsl(164, 86%, 45%) (Accent)
  - Text-align: center
  - Font-style: italic
  - Letter-spacing: 0.8px
```

**Variante: Organisation par Ligne**

Si plusieurs groupes de mots:
```
┌────────────────────────────────┐
│  ┌─────────┐  ┌──────────┐    │  Ligne 1
│  │ Bonjour │  │ monsieur │    │
│  └─────────┘  └──────────┘    │
│                                │
│  ┌──────────────┐              │  Ligne 2
│  │ votre        │              │
│  │ passeport    │              │
│  │ s'il vous    │              │
│  │ plaît        │              │
│  └──────────────┘              │
└────────────────────────────────┘
```

**Container par Ligne**:
```css
.phrase-line {
  Display: flex
  Flex-wrap: wrap
  Justify-content: center
  Gap: 8px
  Margin-bottom: 16px (mb-4)
}
```

### 🎵 Comportement Audio

**Timeline**:
```
1. Vidéo/audio démarre automatiquement (0s)
2. Caption apparaît synchronisée (fade-in)
3. Mots s'illuminent au rythme (highlight)
4. Blocs phonétiques visibles en permanence
5. À la fin (5-12s): boucle OU pause sur dernière frame
```

**Option: Bouton Rejouer** (coin supérieur droit sous header):
```css
Position: Absolute, top: 90px, right: 24px
Size: 48x48px
Background: rgba(255, 255, 255, 0.2) + backdrop-blur-sm
Border-radius: 50%
Border: 2px solid rgba(255, 255, 255, 0.5)
Z-index: 30

Icône: Rotate-CW (replay) 24x24px, white

Hover:
  - Background: rgba(255, 255, 255, 0.3)
  - Transform: rotate(180deg) + scale(1.1)
  - Transition: all 0.4s ease
```

### 🔄 Transition Automatique

Après 2 répétitions automatiques de l'audio (ou 12s max):
```
1. Fade-out de tous les éléments (500ms)
2. Scroll snap vers action suivante (800ms)
```

Ou avec bouton "Continuer" optionnel en bas:
```css
Position: Bottom center, au-dessus du footer
Bottom: 140px
Width: 200px
Height: 44px (h-11)
Background: white
Border-radius: 22px (rounded-full)
Font: 14px, Bold
Color: #1F2937
Text: "Continuer →"
Box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2)
```

---

## Action 3 - Quiz (Text / Image / Audio)

### 📊 Structure Globale des Quiz

Les quiz se déclinent en 3 types différents selon le contexte pédagogique, mais partagent une structure visuelle cohérente.

```
Types de Quiz:
├── Text Quiz: Question texte → Réponses texte
├── Image Quiz: Image → Réponses texte
└── Audio Quiz: Audio → Réponses texte (mots entendus)
```

---

### 3.1. Text Quiz

```
┌────────────────────────────────────────────┐
│ [Mission N]                    [QUIZ]      │ ← Header
│                                            │
│                                            │
│        Que signifie "passeport" ?          │ ← Question
│                                            │
│     ┌────────────────────────────┐        │
│     │  A) 🛂  Document de voyage │        │
│     └────────────────────────────┘        │
│                                            │
│     ┌────────────────────────────┐        │
│     │  B) 🎫  Billet d'avion     │        │
│     └────────────────────────────┘        │
│                                            │
│     ┌────────────────────────────┐        │
│     │  C) 💼  Valise             │        │
│     └────────────────────────────┘        │
│                                            │
│            [Valider]                       │
│                                            │
│ Épisode 1 - Contrôle des passeports       │ ← Footer
│ Testez votre compréhension                │
└────────────────────────────────────────────┘
```

#### 🎨 Background

**Type**: Dégradé simple selon niveau  
**Gradient**: 
- Débutant: `linear-gradient(135deg, #3B82F6 0%, #60A5FA 100%)`
- Intermédiaire: `linear-gradient(135deg, #F97316 0%, #FBBF24 100%)`
- Avancé: `linear-gradient(135deg, #EC4899 0%, #F472B6 100%)`

#### ❓ Question

**Container**:
```css
Text-align: center
Padding: 0 32px (px-8)
Margin-top: 100px (mt-24)
Margin-bottom: 40px (mb-10)
```

**Texte**:
```css
Font: 24px (text-2xl), Bold (700)
Color: white
Line-height: 1.3
Text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3)
Max-width: 90%
Margin: 0 auto
```

#### 📋 Options de Réponse (Text Quiz)

**Container des Options**:
```css
Padding: 0 24px (px-6)
Display: Flex, column
Gap: 14px (gap-3.5)
Margin-bottom: 32px (mb-8)
```

**Chaque Option**:
```css
État Normal:
  - Background: rgba(255, 255, 255, 0.98)
  - Border: 3px solid transparent
  - Border-radius: 20px (rounded-2xl)
  - Padding: 18px 20px (px-5 py-4.5)
  - Display: Flex, align-items center
  - Gap: 14px (gap-3.5)
  - Box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12)
  - Cursor: pointer
  - Transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1)

État Hover:
  - Transform: translateY(-3px)
  - Box-shadow: 0 8px 24px rgba(0, 0, 0, 0.18)
  - Border: 3px solid rgba(28, 183, 154, 0.3)
  
État Sélectionné:
  - Border: 3px solid hsl(164, 86%, 45%)
  - Background: rgba(28, 183, 154, 0.08)
  - Transform: scale(1.02)
  - Box-shadow: 0 6px 20px rgba(28, 183, 154, 0.25)
```

**Structure d'une Option**:
```
┌──────────────────────────────────────┐
│ [Lettre] [Icône] Texte de la réponse │
└──────────────────────────────────────┘
```

**Lettre (A, B, C, D)**:
```css
Font: 18px (text-lg), Bold (700)
Color: #1F2937
Width: 32px
Height: 32px
Display: flex, center aligned
Background: #F3F4F6
Border-radius: 8px (rounded-lg)
Flex-shrink: 0
```

**Icône** (optionnelle):
```css
Size: 28x28px
Emoji ou Lucide icon
Margin-right: 4px
Flex-shrink: 0
```

**Texte**:
```css
Font: 16px (text-base), Medium (500)
Color: #1F2937
Line-height: 1.5
Flex: 1
```

---

### 3.2. Image Quiz

```
┌────────────────────────────────────────────┐
│ [Mission N]                    [QUIZ]      │
│                                            │
│                                            │
│      ┌──────────────────────────┐         │
│      │                          │         │
│      │    [Image du Quiz]       │         │ ← Image centrale
│      │                          │         │
│      └──────────────────────────┘         │
│                                            │
│      Qu'est-ce que c'est ?                │ ← Question
│                                            │
│     ┌────────────────────────────┐        │
│     │  A) Un passeport           │        │
│     └────────────────────────────┘        │
│     ┌────────────────────────────┐        │
│     │  B) Une carte d'identité   │        │
│     └────────────────────────────┘        │
│     ┌────────────────────────────┐        │
│     │  C) Un permis de conduire  │        │
│     └────────────────────────────┘        │
│                                            │
│            [Valider]                       │
│                                            │
│ Épisode 1 - Contrôle des passeports       │
└────────────────────────────────────────────┘
```

#### 🖼️ Image du Quiz

**Container Image**:
```css
Position: Relative
Width: calc(100% - 64px)
Max-width: 320px
Margin: 80px auto 32px (mt-20 mx-auto mb-8)
Aspect-ratio: 4/3
Border-radius: 24px (rounded-3xl)
Overflow: hidden
Box-shadow: 0 12px 40px rgba(0, 0, 0, 0.2)
Background: white
```

**Image Element**:
```css
Width: 100%
Height: 100%
Object-fit: cover
Border: 4px solid white
```

**Effet Visuel** (optionnel):
```css
Border: 4px solid white
Box-shadow (inner): inset 0 0 0 1px rgba(0, 0, 0, 0.1)

Animation entrée:
  - Scale: 0.9 → 1
  - Opacity: 0 → 1
  - Duration: 500ms ease-out
```

#### ❓ Question (sous l'image)

**Container**:
```css
Text-align: center
Padding: 0 32px (px-8)
Margin-bottom: 28px (mb-7)
```

**Texte**:
```css
Font: 20px (text-xl), Bold (700)
Color: white
Text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3)
```

#### 📋 Options de Réponse

Identique au Text Quiz, mais sans icônes emoji (seulement lettre + texte).

---

### 3.3. Audio Quiz

```
┌────────────────────────────────────────────┐
│ [Mission N]                    [QUIZ]      │
│                                            │
│                                            │
│              ┌───────────┐                │
│              │     🔊     │                │ ← Icône audio
│              │           │                │
│              │  [Tap]    │                │
│              └───────────┘                │
│                                            │
│       Quels mots avez-vous entendus ?     │ ← Question
│                                            │
│     ┌────────────────────────────┐        │
│     │  A) Bonjour monsieur       │        │
│     └────────────────────────────┘        │
│     ┌────────────────────────────┐        │
│     │  B) Bonsoir madame         │        │
│     └────────────────────────────┘        │
│     ┌────────────────────────────┐        │
│     │  C) Salut mon ami          │        │
│     └────────────────────────────┘        │
│                                            │
│            [Valider]                       │
│                                            │
│ Épisode 1 - Contrôle des passeports       │
└────────────────────────────────────────────┘
```

#### 🔊 Bouton Audio Central

**Container**:
```css
Position: Center horizontal, margin-top
Width: 140px
Height: 140px
Margin: 100px auto 40px (mt-24 mx-auto mb-10)
```

**Cercle Audio**:
```css
État Normal:
  - Size: 140x140px
  - Background: rgba(255, 255, 255, 0.95) + backdrop-blur-sm
  - Border-radius: 50%
  - Border: 4px solid white
  - Box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25)
  - Display: flex, center aligned
  - Cursor: pointer
  - Position: relative

État Hover:
  - Transform: scale(1.08)
  - Box-shadow: 0 16px 48px rgba(0, 0, 0, 0.3)
  - Transition: all 0.3s ease
```

**Icône Speaker**:
```css
Size: 56x56px
Color: hsl(164, 86%, 45%) (Accent)
Stroke-width: 2.5px
Lucide icon: Volume2 ou Speaker
```

**État Lecture (en train de jouer)**:
```css
Animation:
  1. Border pulse: 4px → 6px → 4px (1s loop)
  2. Border-color: white → Accent → white
  3. Ondes sonores (3 rings concentriques):
     - Border: 3px solid rgba(28, 183, 154, 0.4)
     - Animation: scale(1) → scale(1.8) + opacity(1) → opacity(0)
     - Durée: 1.5s infinite
     - Stagger: 0s, 0.5s, 1s
```

**Indicateur de Lecture**:
```css
Position: Below button
Margin-top: 12px (mt-3)
Font: 13px, Medium (500)
Color: white
Text: "🔊 Écoute..." 
Animation: Fade pulse
```

**Bouton Replay** (après 1ère écoute):
```css
Position: Top-right du cercle
Size: 40x40px
Background: white
Border-radius: 50%
Border: 2px solid Accent
Box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15)
Icône: Rotate-CW (20x20px, Accent)
Z-index: 5

Hover:
  - Transform: rotate(180deg)
  - Background: Accent
  - Icône color: white
```

#### ❓ Question

**Container**:
```css
Text-align: center
Padding: 0 32px (px-8)
Margin-bottom: 36px (mb-9)
```

**Texte**:
```css
Font: 21px (text-xl), Bold (700)
Color: white
Text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3)
Line-height: 1.4
```

#### 📋 Options de Réponse

Identique au Text Quiz.

---

### ✅ Feedback Visuel (Commun aux 3 Types)

**Réponse Correcte**:
```
Animation:
  1. Border: 3px solid #22C55E (vert vif)
  2. Background: rgba(34, 197, 94, 0.12)
  3. Checkmark icon apparaît (fade + scale):
     - Position: Absolute, right: 16px
     - Size: 32x32px
     - Background: #22C55E (cercle)
     - Icône: ✓ white (20x20px)
     - Animation: scale(0) → scale(1.2) → scale(1) (400ms)
  4. Success shine effect:
     - Gradient sweep de gauche à droite
     - 200ms duration
  5. Confetti (optionnel): 20 particules légères
  6. Son: "success-ding.mp3" (150ms)
  7. Vibration: 50ms

Durée totale: 1.2s puis auto-transition
```

**Réponse Incorrecte**:
```
Animation Réponse Sélectionnée (Mauvaise):
  1. Border: 3px solid #EF4444 (rouge)
  2. Background: rgba(239, 68, 68, 0.12)
  3. X icon apparaît:
     - Position: Absolute, right: 16px
     - Size: 32x32px
     - Background: #EF4444 (cercle)
     - Icône: ✕ white (20x20px)
  4. Shake animation:
     - translateX: 0 → -8px → 8px → -8px → 0
     - Duration: 400ms
     - Easing: ease-in-out

Animation Bonne Réponse (Révélée):
  1. Border: 3px solid #22C55E
  2. Background: rgba(34, 197, 94, 0.12)
  3. Checkmark apparaît (fade-in)
  4. Glow pulse effect:
     - Box-shadow: 0 0 0 0 rgba(34, 197, 94, 0.7)
       → 0 0 0 12px rgba(34, 197, 94, 0)
     - Duration: 600ms

Autres Réponses:
  - Opacity: 0.35
  - Pointer-events: none
  - Filter: grayscale(0.5)

Audio & Haptique:
  - Son: "error-buzz.mp3" (100ms)
  - Vibration: 100ms pause 50ms 100ms

Durée totale: 1.8s puis auto-transition
```

### 🔘 Bouton Valider (Commun)

**Style**:
```css
Position: Center horizontal
Width: calc(100% - 80px)
Max-width: 280px
Height: 52px (h-13)
Background: hsl(164, 86%, 45%)
Border: none
Border-radius: 26px (rounded-full)
Font: 16px, Bold (700)
Color: white
Box-shadow: 0 6px 20px rgba(28, 183, 154, 0.35)
Cursor: pointer
Transition: all 0.3s ease
Margin: 0 auto

État Disabled (aucune sélection):
  - Background: #94A3B8 (gris)
  - Opacity: 0.6
  - Cursor: not-allowed
  - Box-shadow: none

État Hover (enabled):
  - Transform: scale(1.05) translateY(-2px)
  - Box-shadow: 0 8px 28px rgba(28, 183, 154, 0.45)
  - Background: #17A085 (plus foncé)

État Active (click):
  - Transform: scale(0.98)
  - Box-shadow: 0 4px 12px rgba(28, 183, 154, 0.3)
```

---

## Action 4 - Speaking Task

### 🎤 Structure Visuelle

```
┌────────────────────────────────────────────┐
│ [Mission N]              [SPEAKING]        │ ← Header
│                                            │
│      [🔊 Bouton Écouter]                  │ ← Nouveau: Audio exemple
│                                            │
│          Répétez la phrase :               │
│                                            │
│       "Voici mon passeport"                │
│                                            │
│                                            │
│              ┌─────────┐                  │
│              │    🎤    │                  │ ← Bouton micro principal
│              │         │                  │
│              │  [Tap]  │                  │
│              └─────────┘                  │
│                                            │
│         Appuyez pour parler                │
│                                            │
│                                            │
│                                            │
│ Épisode 1 - Contrôle des passeports       │ ← Footer
│ Pratiquez votre prononciation              │
└────────────────────────────────────────────┘
```

### 🎨 Background

**Type**: Dégradé animé avec effet subtil  
**Gradient Base**: Selon niveau (identique aux autres actions)
**Animation Idle**: Gradient pulse très subtil (4s loop, opacity: 1 ↔ 0.95)

### 🔊 **NOUVEAU: Bouton "Écouter l'Exemple"**

**Position**: En haut, sous le header, centré horizontalement  
**Margin-top**: 80px (mt-20)

**Container**:
```css
Display: flex, center aligned
Width: fit-content
Margin: 80px auto 32px (mt-20 mx-auto mb-8)
Padding: 14px 28px (px-7 py-3.5)
Background: rgba(255, 255, 255, 0.95) + backdrop-blur-md
Border: 2px solid rgba(255, 255, 255, 0.6)
Border-radius: 30px (rounded-full)
Box-shadow: 0 6px 24px rgba(0, 0, 0, 0.15)
Cursor: pointer
Gap: 12px (gap-3)
Transition: all 0.3s ease
```

**Icône Speaker**:
```css
Lucide icon: Volume2
Size: 24x24px
Color: hsl(164, 86%, 45%)
Stroke-width: 2.5px
```

**Texte**:
```css
Font: 15px (text-base), Semi-Bold (600)
Color: #1F2937
White-space: nowrap
Text: "Écouter l'exemple"
```

**État Hover**:
```css
Transform: scale(1.05) translateY(-2px)
Box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2)
Background: white
Border-color: hsl(164, 86%, 45%)
```

**État Lecture** (audio en cours):
```css
Background: rgba(28, 183, 154, 0.15)
Border: 2px solid hsl(164, 86%, 45%)

Icône animation:
  - Pulse scale: 1 ↔ 1.15 (500ms loop)
  - Color: hsl(164, 86%, 45%)

Texte change: "Écoute en cours..."

Ondes sonores (optionnel):
  - 2 rings autour du bouton
  - Border: 2px solid rgba(28, 183, 154, 0.3)
  - Animation: scale(1) → scale(1.4) + fade-out
  - Duration: 1.2s infinite
```

### 📢 Instruction

**Container**:
```css
Text-align: center
Padding: 0 32px (px-8)
Margin-bottom: 20px (mb-5)
```

**Texte "Répétez la phrase :"**:
```css
Font: 16px (text-base), Semi-Bold (600)
Color: rgba(255, 255, 255, 0.9)
Margin-bottom: 16px (mb-4)
Text-transform: uppercase
Letter-spacing: 1px
```

**Phrase à Répéter**:
```css
Font: 26px (text-3xl), Bold (700)
Color: white
Line-height: 1.35
Text-shadow: 0 3px 12px rgba(0, 0, 0, 0.4)
Background: rgba(0, 0, 0, 0.25) + backdrop-blur-sm
Padding: 20px 32px (px-8 py-5)
Border-radius: 20px (rounded-2xl)
Border: 2px solid rgba(255, 255, 255, 0.2)
Display: inline-block
Max-width: 85%
```

### 🎙️ Bouton Microphone Principal

**Container**:
```css
Position: Relative
Display: flex, center aligned
Justify-content: center
Margin: 60px auto 24px (my-16 mx-auto mb-6)
```

**Cercle Microphone**:
```css
État Normal (Idle):
  - Size: 140x140px
  - Background: rgba(255, 255, 255, 0.98)
  - Border-radius: 50%
  - Border: 4px solid white
  - Box-shadow: 0 12px 48px rgba(0, 0, 0, 0.25)
  - Cursor: pointer
  - Display: flex, center aligned
  - Position: relative
  - Z-index: 10
  
État Hover:
  - Transform: scale(1.08)
  - Box-shadow: 0 16px 56px rgba(0, 0, 0, 0.3)
  - Border-width: 5px
  - Transition: all 0.3s ease
```

**Icône Microphone**:
```css
Lucide Icon: Mic
Size: 60x60px (très grande)
Color: hsl(164, 86%, 45%)
Stroke-width: 2.5px
```

**État Enregistrement**:
```css
Animation de Démarrage (transition):
  1. Scale: 1 → 0.92 → 1.12 → 1 (400ms cubic-bezier)
  2. Background transition: white → rgba(239, 68, 68, 0.95) (300ms)
  3. Border-color: white → #EF4444
  4. Icône color: Accent → white
  5. Vibration haptique: 100ms

Pendant Enregistrement:
  1. Icône pulse continue:
     - Transform: scale(1) ↔ scale(1.2)
     - Duration: 600ms
     - Loop: infinite
     - Easing: ease-in-out
  
  2. Cercle glow pulse:
     - Box-shadow: 0 12px 48px rgba(239, 68, 68, 0.4)
       → 0 16px 64px rgba(239, 68, 68, 0.6)
     - Duration: 1s loop
  
  3. Ondes Sonores (rings):
     - 3 cercles concentriques
     - Position: absolute, centrés sur le bouton
     - Border: 3px solid rgba(239, 68, 68, 0.5)
     - Border-radius: 50%
     - Animation: pulse-ring (voir ci-dessous)
     - Stagger delay: 0ms, 400ms, 800ms
```

**Ondes Sonores (Rings) Animation**:
```css
@keyframes pulse-ring {
  0% {
    width: 140px;
    height: 140px;
    opacity: 1;
    border-width: 3px;
  }
  50% {
    opacity: 0.6;
  }
  100% {
    width: 280px;
    height: 280px;
    opacity: 0;
    border-width: 1px;
  }
}

.sound-ring {
  position: absolute;
  border-radius: 50%;
  border: 3px solid rgba(239, 68, 68, 0.5);
  pointer-events: none;
  animation: pulse-ring 1.8s cubic-bezier(0.4, 0, 0.2, 1) infinite;
}

.sound-ring:nth-child(2) {
  animation-delay: 0.4s;
}

.sound-ring:nth-child(3) {
  animation-delay: 0.8s;
}
```

### ⏱️ Timer d'Enregistrement

**Position**: En dessous du bouton micro  
**Margin-top**: 20px (mt-5)

**Style**:
```css
Font: 36px (text-4xl), Bold (700), Mono (Courier New)
Color: white
Text-align: center
Text-shadow: 0 2px 12px rgba(0, 0, 0, 0.5)
Letter-spacing: 2px
Min-width: 120px

Animation par Seconde:
  - Transform: scale(1) → scale(1.15) → scale(1)
  - Duration: 300ms
  - Trigger: chaque seconde
```

**Format**: 
- "00:00" → "00:05" → "00:20" (max 20 secondes)
- Color change:
  - 0-10s: white
  - 11-15s: #FBBF24 (jaune, warning)
  - 16-20s: #EF4444 (rouge, urgent)

**Label sous le timer**:
```css
Font: 13px (text-sm), Medium (500)
Color: rgba(255, 255, 255, 0.8)
Text: "Enregistrement en cours..."
Margin-top: 8px (mt-2)
```

### 📊 Analyse et Feedback

**Écran de Transition (Analyse en cours)**:
```
Overlay: rgba(0, 0, 0, 0.9) + backdrop-blur-xl
Z-index: 100
Animation: Fade-in 300ms

┌─────────────────────────────────┐
│                                 │
│   Analyse de votre             │
│   prononciation...             │
│                                 │
│   [Spinner animation]           │
│   [Progress bar]                │
│                                 │
│   🤖 IA en cours d'analyse     │
│                                 │
└─────────────────────────────────┘
```

**Container Analyse**:
```css
Position: Fixed, center
Width: calc(100% - 64px)
Max-width: 360px
Background: transparent
Text-align: center
Z-index: 101
```

**Spinner**:
```css
Size: 80x80px
Border: 6px solid rgba(255, 255, 255, 0.2)
Border-top-color: hsl(164, 86%, 45%)
Border-radius: 50%
Animation: spin 1s linear infinite
Margin: 0 auto 24px

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

**Texte "Analyse..."**:
```css
Font: 20px (text-xl), Bold (700)
Color: white
Margin-bottom: 32px (mb-8)
```

**Progress Bar**:
```css
Width: 100%
Height: 6px
Background: rgba(255, 255, 255, 0.2)
Border-radius: 3px
Overflow: hidden
Margin-bottom: 20px (mb-5)

Fill:
  - Background: linear-gradient(90deg, 
      hsl(164, 86%, 45%), 
      hsl(164, 86%, 55%))
  - Height: 100%
  - Animation: width 0% → 100% (1.8s ease-out)
  - Border-radius: 3px
```

**Label IA**:
```css
Font: 14px (text-sm), Medium (500)
Color: rgba(255, 255, 255, 0.7)
Display: flex, center aligned
Gap: 8px (gap-2)
Justify-content: center
```

**Durée Totale**: 1.8 - 2.2 secondes (simulation analyse)

---

**Résultats d'Analyse (Modal)**:
```
┌──────────────────────────────────────────┐
│                                          │
│           Excellent ! 🎉                 │
│                                          │
│  ┌──────────────────────────────────┐  │
│  │  Prononciation          92%      │  │
│  │  ██████████████████░░            │  │
│  └──────────────────────────────────┘  │
│                                          │
│  ┌──────────────────────────────────┐  │
│  │  Rythme                 88%      │  │
│  │  █████████████████░░░            │  │
│  └──────────────────────────────────┘  │
│                                          │
│  ┌──────────────────────────────────┐  │
│  │  Intonation             85%      │  │
│  │  █████████████████░░░░           │  │
│  └──────────────────────────────────┘  │
│                                          │
│  💡 Points à améliorer:                 │
│  • Son "r" dans "passeport"             │
│  • Liaison "mon" + "passeport"          │
│                                          │
│  ┌─────────────┐  ┌────────────────┐   │
│  │  Réessayer  │  │  Continuer ✓   │   │
│  └─────────────┘  └────────────────┘   │
│                                          │
└──────────────────────────────────────────┘
```

**Card Résultats**:
```css
Position: Fixed, center
Width: calc(100% - 48px)
Max-width: 420px
Background: white
Border-radius: 32px (rounded-3xl)
Padding: 36px (p-9)
Box-shadow: 0 24px 80px rgba(0, 0, 0, 0.35)
Z-index: 102
Animation: scale-in + fade-in (500ms ease-out)

@keyframes scale-in {
  0% {
    transform: scale(0.85);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}
```

**Titre Feedback** (selon score):
```css
Font: 26px (text-3xl), Bold (700)
Text-align: center
Margin-bottom: 28px (mb-7)
Display: flex, center aligned
Gap: 12px

Variations:
  85-100%: 
    Color: #22C55E
    Text: "Excellent ! 🎉"
  
  70-84%:
    Color: #FBBF24
    Text: "Très bien ! 👍"
  
  50-69%:
    Color: #F97316
    Text: "Bien ! 💪"
  
  0-49%:
    Color: #EF4444
    Text: "À améliorer 📚"
```

**Métriques (3 barres)** - Prononciation, Rythme, Intonation

**Boutons d'Action**:
```css
Container:
  - Display: flex
  - Gap: 14px (gap-3.5)
  - Margin-top: 28px (mt-7)

[Réessayer]:
  - Flex: 1
  - Height: 52px (h-13)
  - Background: transparent
  - Border: 2px solid #E5E7EB
  - Border-radius: 14px (rounded-2xl)
  - Font: 15px (text-base), Semi-Bold (600)
  - Color: #6B7280

[Continuer]:
  - Flex: 1
  - Height: 52px (h-13)
  - Background: hsl(164, 86%, 45%)
  - Border-radius: 14px (rounded-2xl)
  - Font: 15px (text-base), Bold (700)
  - Color: white
  - Box-shadow: 0 6px 20px rgba(28, 183, 154, 0.35)
```

---

## Transitions entre Actions

### 🔄 Comportement de Scroll

**Scroll Snap**:
```css
.feed-container {
  scroll-snap-type: y mandatory;
  scroll-behavior: smooth;
  overflow-y: scroll;
  overflow-x: hidden;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  height: calc(100vh - 73px - 66px);
}

.action-card {
  scroll-snap-align: start;
  scroll-snap-stop: always;
  height: calc(100vh - 73px - 66px);
  position: relative;
}
```

### ⏭️ Auto-Transition (après feedback)

**Timeline Générale**:
```
1. Utilisateur complète l'action correctement
2. Feedback visuel apparaît (1-1.8s selon type)
3. Délai de lecture/assimilation (0.8-1.5s)
4. Fade out du feedback (400ms)
5. Scroll automatique vers action suivante (800ms ease-in-out)
6. Nouvelle action fade in (400ms)
```

**Durées par Type d'Action**:
```
Video Quiz:
  - Feedback: 1.2s (correct) / 1.8s (incorrect)
  - Total avant transition: 2-2.6s

Voice Spell:
  - Lecture vidéo complète (5-12s)
  - Auto-loop 2 fois OU bouton "Continuer"

Quiz (Text/Image/Audio):
  - Feedback: 1.2s (correct) / 1.8s (incorrect)
  - Total avant transition: 2.2-2.8s

Speaking Task:
  - Feedback détaillé: utilisateur contrôle
  - Clique "Continuer" pour avancer
```

### 🎯 Indicateur de Progression

**Position**: Top center, sous le header  
**Z-index**: 50

```
┌──────────────────────┐
│   Séquence 2 / 4     │
│   ████████░░░░░░░░   │
└──────────────────────┘
```

**Container**:
```css
Position: Fixed
Top: 80px
Left: 50%
Transform: translateX(-50%)
Background: rgba(0, 0, 0, 0.5) + backdrop-blur-md
Padding: 10px 20px (px-5 py-2.5)
Border-radius: 24px (rounded-full)
Z-index: 50
Box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2)
Min-width: 160px
```

---

**Version**: 2.0 (Updated)  
**Dernière mise à jour**: Novembre 2025  
**Statut**: ✅ Ready for Implementation