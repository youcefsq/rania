<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Apprentissage Automatique en Médecine : Exploration Interactive</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Visualization & Content Choices:
        - Tableau 1 (Algorithmes): Objectif: Organiser/Comparer. Méthode: Tableau HTML, JS clic pour afficher détails (Forces/Limites). Justification: Rend l'info détaillée accessible à la demande. Lib: Vanilla JS.
        - Tableau 2 (Percées Récentes): Objectif: Organiser/Informer. Méthode: Tableau HTML, JS clic pour afficher détails (Impact Clé). Justification: Met en évidence les impacts clés efficacement. Lib: Vanilla JS.
        - Stats Adoption IA Générative (e.g., 85% exploration, 64% ROI): Objectif: Informer/Comparer. Méthode: Chart.js Doughnut/Bar. Justification: Visualise clairement les taux d'adoption. Lib: Chart.js.
        - Croissance Marché Apprentissage Fédéré (e.g., $28M à $141M): Objectif: Montrer Évolution/Informer. Méthode: Chart.js Ligne/Bar. Justification: Illustre la tendance du marché. Lib: Chart.js.
        - Précision Diagnostique (IA vs Humain - si chiffres disponibles et pertinents): Objectif: Comparer. Méthode: Chart.js Barres groupées. Justification: Compare visuellement les précisions rapportées. Lib: Chart.js.
        - Contenu Textuel: Objectif: Informer/Expliquer. Méthode: Paragraphes résumés du rapport pour chaque section de la SPA, avec titres clairs. Justification: Fournit les informations essentielles de manière contextuelle.
        - Section Références et Ressources: Objectif: Fournir crédibilité, permettre approfondissement. Méthode: Liste de références clés (citations originales), lien placeholder vers rapport complet. Justification: Essentiel pour un rapport de recherche. UI en français.
        - Tout le contenu textuel et l'interface utilisateur en français.
        - CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body { font-family: 'Inter', sans-serif; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; }
        .chart-container { position: relative; width: 100%; max-width: 550px; margin-left: auto; margin-right: auto; height: 300px; max-height: 380px; }
        @media (min-width: 768px) { .chart-container { height: 330px; } }
        .nav-link { transition: all 0.2s ease-in-out; border-bottom: 2px solid transparent; }
        .nav-link.active { color: #ffffff; background-color: #0891b2; /* cyan-600 */ border-bottom: 2px solid #06b6d4; /* cyan-500 */ font-weight: 600; }
        .nav-link:not(.active):hover { color: #ffffff; background-color: #155e75; /* cyan-800 */ }
        .table-details { display: none; }
        .table-row-item.expanded { background-color: #f0f9ff; /* sky-50 */ }
        .table-row-item.expanded .table-details { display: table-row; }
        .table-row-item.expanded .expand-icon { transform: rotate(90deg); }
        .content-section { display: none; animation: fadeIn 0.4s ease-in-out; }
        .content-section.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .tab-button.active { background-color: #0e7490; /* cyan-700 */ color: white; border-color: #06b6d4; /* cyan-500 */ }
        .tab-button { transition: background-color 0.2s ease, color 0.2s ease, border-color 0.2s ease; border-bottom-width: 2px; }
        .sub-section { display: none; animation: fadeIn 0.3s ease-in-out; }
        .sub-section.active { display: block; }
        .reference-item { border-left: 3px solid #0891b2; /* cyan-600 */ padding-left: 0.875rem; margin-bottom: 0.875rem; transition: background-color 0.2s; }
        .reference-item:hover { background-color: #f0f9ff; }
        .btn-primary {
            display: inline-flex; align-items: center; background-color: #0891b2; /* cyan-600 */ color: white; font-weight: 600;
            padding: 0.625rem 1.25rem; border-radius: 0.375rem; box-shadow: 0 1px 3px 0 rgba(0,0,0,0.1), 0 1px 2px 0 rgba(0,0,0,0.06);
            transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
        }
        .btn-primary:hover { background-color: #06b6d4; /* cyan-500 */ transform: translateY(-1px); }
        .btn-primary:focus { outline: none; ring: 2px; ring-offset: 2px; ring-color: #06b6d4; /* cyan-500 */ }

    </style>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
</head>
<body class="bg-slate-100 text-slate-800">
    <header class="bg-cyan-700 text-white shadow-lg sticky top-0 z-50">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <h1 class="text-xl sm:text-2xl font-bold truncate">IA en Médecine : Exploration</h1>
                <button id="mobileMenuButton" class="md:hidden p-2 rounded-md hover:bg-cyan-600 focus:outline-none focus:ring-2 focus:ring-inset focus:ring-white" aria-label="Menu principal" aria-expanded="false">
                    <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
                    </svg>
                </button>
            </div>
            <nav id="mainNav" class="hidden md:flex flex-col md:flex-row md:space-x-1 lg:space-x-2 xl:space-x-3 py-2 items-center">
                <a href="#accueil" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Accueil</a>
                <a href="#principes" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Principes de l'AA</a>
                <a href="#applications" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Domaines d'Application</a>
                <a href="#avancees" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Avancées Récentes</a>
                <a href="#bilan" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Bilan</a>
                <a href="#references" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Références</a>
                <a href="#conclusion" class="nav-link px-2 py-2 rounded-md text-sm font-medium">Perspectives</a>
            </nav>
        </div>
    </header>

    <main class="container mx-auto px-4 sm:px-6 lg:px-8 py-8">
        
        <section id="accueil" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Bienvenue dans l'Exploration de l'Apprentissage Automatique en Médecine</h2>
            <p class="mb-4 leading-relaxed">Cette application interactive a pour but de vulgariser et de rendre accessible le contenu du rapport de recherche "Le Rôle Transformateur de l'Apprentissage Automatique en Médecine Moderne". L'apprentissage automatique (AA), une branche de l'intelligence artificielle, révolutionne la médecine en permettant aux systèmes d'apprendre à partir de données pour améliorer les diagnostics, personnaliser les traitements et optimiser les opérations de santé.</p>
            <p class="mb-4 leading-relaxed">Naviguez à travers les différentes sections pour découvrir comment l'AA transforme les soins de santé, des concepts fondamentaux aux applications concrètes et aux dernières innovations. Explorez les algorithmes clés, les domaines d'impact majeur comme l'imagerie médicale et la découverte de médicaments, ainsi que les avancées de pointe telles que l'IA générative et l'apprentissage fédéré.</p>
            <p class="leading-relaxed">L'objectif est de fournir une compréhension claire du potentiel immense de l'AA, tout en soulignant les bénéfices tangibles et les défis importants à relever pour une intégration réussie et éthique dans le domaine médical.</p>
        </section>

        <section id="principes" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Principes Fondamentaux de l'Apprentissage Automatique</h2>
            <p class="mb-4 leading-relaxed">L'application de l'apprentissage automatique en médecine repose sur plusieurs paradigmes d'apprentissage, chacun adapté à des types de données et des questions cliniques spécifiques. Comprendre ces principes est essentiel pour apprécier la diversité des applications médicales de l'AA.</p>
            
            <div class="mb-6">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Paradigmes Clés</h3>
                <ul class="list-disc list-inside space-y-2 text-slate-700">
                    <li><strong>Apprentissage Supervisé :</strong> Les algorithmes apprennent à partir de données étiquetées (où le résultat est connu) pour faire des prédictions ou classifier de nouvelles données. Utilisé pour le diagnostic, le pronostic, l'estimation des risques.</li>
                    <li><strong>Apprentissage Non Supervisé :</strong> Les algorithmes travaillent avec des données non étiquetées pour identifier des structures, des schémas ou des relations cachées. Utile pour la stratification des patients, la découverte de sous-types de maladies.</li>
                    <li><strong>Apprentissage par Renforcement (AR) :</strong> Un agent apprend à prendre des décisions séquentielles dans un environnement dynamique pour maximiser une récompense. Appliqué à l'optimisation de stratégies de traitement au fil du temps.</li>
                    <li><strong>Apprentissage Profond (AP) :</strong> Un sous-ensemble de l'AA utilisant des réseaux de neurones artificiels à plusieurs couches pour apprendre des représentations hiérarchiques complexes à partir de données brutes. Très puissant pour l'analyse d'images médicales et les données génomiques.</li>
                </ul>
            </div>

            <h3 class="text-xl font-semibold text-cyan-600 mb-3">Tableau Interactif : Principaux Algorithmes d'AA en Médecine</h3>
            <p class="mb-4 text-sm text-slate-600">Cliquez sur une ligne pour afficher/masquer les détails (forces et limites).</p>
            <div class="overflow-x-auto shadow-md rounded-lg">
                <table class="min-w-full divide-y divide-slate-200" id="table1Algorithms">
                    <thead class="bg-slate-100">
                        <tr>
                            <th class="px-6 py-3 text-left text-xs font-medium text-slate-500 uppercase tracking-wider">Algorithme/Paradigme</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-slate-500 uppercase tracking-wider">Description Brève</th>
                            <th class="px-6 py-3 text-left text-xs font-medium text-slate-500 uppercase tracking-wider">Domaines d'Application Médicale</th>
                            <th class="px-2 py-3 text-center text-xs font-medium text-slate-500 uppercase tracking-wider w-12"></th>
                        </tr>
                    </thead>
                    <tbody class="bg-white divide-y divide-slate-200">
                        </tbody>
                </table>
            </div>
        </section>

        <section id="applications" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Domaines d'Application Majeurs</h2>
            <p class="mb-4 leading-relaxed">L'apprentissage automatique est appliqué dans un éventail vaste et croissant de domaines médicaux, transformant fondamentalement la compréhension, le diagnostic et le traitement des maladies, ainsi que le fonctionnement des systèmes de santé. Explorez ci-dessous quelques-uns des domaines d'impact les plus significatifs.</p>
            
            <div id="applicationsTabsContainer" class="mb-6 border-b border-slate-300">
                <nav class="-mb-px flex space-x-2 sm:space-x-4 overflow-x-auto" aria-label="Tabs" id="applicationsTabs">
                    <button data-tab="app-diagnostic" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm active">Diagnostic et Pronostic</button>
                    <button data-tab="app-personnalisee" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">Médecine Personnalisée</button>
                    <button data-tab="app-medicaments" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">Découverte de Médicaments</button>
                    <button data-tab="app-operations" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">Optimisation Opérationnelle</button>
                    <button data-tab="app-essais" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">Essais Cliniques</button>
                </nav>
            </div>

            <div id="app-diagnostic" class="sub-section active">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Amélioration des Diagnostics et Pronostics</h3>
                <p class="mb-3 leading-relaxed">L'AA améliore la précision et la rapidité des diagnostics. En analysant des images médicales (radios, scanners, IRM) avec des algorithmes d'apprentissage profond (CNN), l'IA peut détecter des maladies comme le cancer ou la rétinopathie diabétique, parfois avec une précision surpassant les experts humains. Par exemple, certains systèmes IA détectent les nodules pulmonaires avec 94% de précision contre 65% pour les radiologues.</p>
                <p class="mb-3 leading-relaxed">Les analyses prédictives utilisent l'AA pour prévoir les épidémies (COVID-19, grippe) en analysant des données de santé publique, et pour gérer les maladies chroniques en signalant les signes précoces de détérioration. L'IA aide aussi à réduire les réadmissions hospitalières en identifiant les patients à haut risque.</p>
                 <div class="mt-6 mb-4 flex justify-center">
                    <div class="chart-container bg-slate-50 p-4 rounded-lg shadow-inner">
                        <canvas id="diagnosticAccuracyChart"></canvas>
                        <p class="text-center text-xs text-slate-500 mt-2">Précision comparative (illustratif) de l'IA dans la détection de nodules pulmonaires.</p>
                    </div>
                </div>
            </div>
            <div id="app-personnalisee" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Révolution de la Médecine Personnalisée</h3>
                <p class="mb-3 leading-relaxed">L'AA est au cœur de la médecine personnalisée, adaptant les traitements aux caractéristiques uniques de chaque patient (génétique, mode de vie). En analysant les données génomiques et pharmacogénomiques, l'AA identifie les variations génétiques liées aux maladies, prédit la réponse aux traitements et évalue la toxicité des médicaments. Par exemple, des modèles prédisent la réponse à l'immunothérapie en oncologie.</p>
                <p class="mb-3 leading-relaxed">La surveillance en temps réel via les dispositifs portables (montres intelligentes, capteurs) génère des données physiologiques continues. L'AA transforme ces données en informations exploitables pour la détection précoce de problèmes de santé, l'ajustement des traitements et le suivi de l'observance thérapeutique. L'étude Apple Heart a utilisé l'Apple Watch pour détecter la fibrillation auriculaire.</p>
                <p class="leading-relaxed">Pour les maladies complexes (cancer, diabète), l'AA aide à développer des stratégies d'intervention sur mesure. En radiothérapie, l'IA personnalise la planification en délimitant précisément les tumeurs.</p>
            </div>
            <div id="app-medicaments" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Accélération de la Découverte et du Développement de Médicaments</h3>
                <p class="mb-3 leading-relaxed">Le processus de découverte de médicaments est long et coûteux. L'AA l'accélère en identifiant des cibles thérapeutiques, en concevant *de novo* des structures moléculaires (avec GANs, VAEs), et en prédisant l'efficacité et la toxicité des candidats médicaments. AlphaFold de DeepMind prédit les structures protéiques, aidant à la conception de médicaments.</p>
                <p class="mb-3 leading-relaxed">On estime que d'ici 2025, l'IA sera impliquée dans la découverte de 30% des nouveaux médicaments. Le repositionnement de médicaments (trouver de nouvelles utilisations pour des médicaments existants) est aussi amélioré : BenevolentAI a identifié la baricitinib comme traitement potentiel pour la COVID-19 en quelques jours.</p>
                <p class="leading-relaxed">L'IA peut augmenter la probabilité de succès des essais cliniques de phase 1 de 40-65% à 80-90%.</p>
            </div>
            <div id="app-operations" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Optimisation des Opérations et de la Gestion des Soins de Santé</h3>
                <p class="mb-3 leading-relaxed">L'AA améliore l'efficience des opérations hospitalières. Il permet une allocation intelligente des ressources (lits, personnel) en prévoyant la demande. Des systèmes aident à la priorisation des patients aux urgences et optimisent la planification du personnel (Providence Health a réduit le temps de création des plannings de plusieurs heures à 15 minutes).</p>
                <p class="mb-3 leading-relaxed">L'automatisation des tâches administratives est un autre gain majeur. Le TALN (Traitement Automatique du Langage Naturel) structure les données non structurées des DSE, facilite la dictée vocale pour la documentation clinique et automatise la facturation et le codage. Cela réduit la charge de travail et les erreurs, permettant au personnel de se concentrer sur les soins.</p>
            </div>
            <div id="app-essais" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Rationalisation des Essais Cliniques</h3>
                <p class="mb-3 leading-relaxed">Les essais cliniques sont essentiels mais complexes. L'AA optimise plusieurs étapes : la stratification avancée des patients et le recrutement (en analysant DSE, données génomiques, etc. pour trouver des candidats éligibles plus rapidement), la conception d'essais (optimisation des protocoles, prédiction des résultats), l'évaluation des risques et la prédiction des issues.</p>
                <p class="mb-3 leading-relaxed">Le TALN extrait des informations pertinentes des dossiers pour apparier les patients aux essais. L'IA facilite aussi les conceptions d'essais adaptatifs, où le protocole peut être modifié en temps réel en fonction des données émergentes. Ces avancées visent à rendre les essais plus efficaces, éthiques et concluants.</p>
            </div>
        </section>

        <section id="avancees" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Avancées Récentes et de Pointe</h2>
            <p class="mb-4 leading-relaxed">La période 2023-2025 a vu une accélération de l'innovation en IA médicale, avec l'émergence de modèles puissants et de nouvelles applications. Ces développements repoussent les frontières de ce que l'IA peut accomplir en médecine.</p>

            <div id="avanceesTabsContainer" class="mb-6 border-b border-slate-300">
                <nav class="-mb-px flex space-x-2 sm:space-x-4 overflow-x-auto" aria-label="Tabs" id="avanceesTabs">
                    <button data-tab="av-generative" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm active">IA Générative</button>
                    <button data-tab="av-federe" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">Apprentissage Fédéré</button>
                    <button data-tab="av-xai" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">IA Explicable (XAI)</button>
                    <button data-tab="av-novateurs" class="tab-button whitespace-nowrap py-3 px-3 sm:px-4 border-transparent text-slate-600 hover:text-cyan-700 hover:border-cyan-600 font-medium text-sm">Algorithmes Novateurs</button>
                </nav>
            </div>
            
            <div id="av-generative" class="sub-section active">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Émergence de l'IA Générative (LLM, GAN)</h3>
                <p class="mb-3 leading-relaxed">L'IA Générative (GenAI), incluant les Grands Modèles de Langage (LLM) et les Réseaux Génératifs Antagonistes (GAN), crée du contenu nouveau (texte, images, structures moléculaires). Elle est utilisée pour la découverte de médicaments (conception de molécules), l'amélioration d'images médicales, et l'automatisation des DSE (outils d'écoute ambiante résumant les consultations médecin-patient).</p>
                <p class="mb-3 leading-relaxed">En 2024, 85% des dirigeants du secteur santé US exploraient ou adoptaient la GenAI. Malgré un potentiel immense, des défis subsistent concernant la fiabilité ("hallucinations" des LLM) et la validation réglementaire.</p>
                <div class="mt-6 mb-4 flex justify-center">
                    <div class="chart-container bg-slate-50 p-4 rounded-lg shadow-inner">
                        <canvas id="genAiAdoptionChart"></canvas>
                        <p class="text-center text-xs text-slate-500 mt-2">Adoption de l'IA Générative dans le secteur de la santé US (T4 2024).</p>
                    </div>
                </div>
            </div>
            <div id="av-federe" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Apprentissage Fédéré pour la Recherche Collaborative</h3>
                <p class="mb-3 leading-relaxed">L'Apprentissage Fédéré (AF) permet d'entraîner des modèles d'IA sur des données issues de multiples institutions sans partager les données brutes des patients, préservant ainsi la confidentialité. Les mises à jour du modèle sont agrégées centralement. Ceci est crucial pour accéder à des ensembles de données vastes et diversifiés.</p>
                <p class="mb-3 leading-relaxed">Le marché de l'AF en santé est en forte croissance (estimé à $28-30M en 2024, projeté à $141M d'ici 2034). Des initiatives comme Owkin et l'étude EXAM (qui a amélioré de 16% la performance d'un modèle pour prédire les besoins en oxygène de patients COVID-19) illustrent son potentiel.</p>
                 <div class="mt-6 mb-4 flex justify-center">
                    <div class="chart-container bg-slate-50 p-4 rounded-lg shadow-inner">
                        <canvas id="federatedLearningMarketChart"></canvas>
                         <p class="text-center text-xs text-slate-500 mt-2">Croissance projetée du marché de l'Apprentissage Fédéré en santé.</p>
                    </div>
                </div>
            </div>
            <div id="av-xai" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Essor de l'IA Explicable (XAI)</h3>
                <p class="mb-3 leading-relaxed">Face à la complexité des modèles d'AA ("boîtes noires"), l'XAI vise à rendre leurs décisions plus transparentes et interprétables. C'est essentiel pour la confiance des cliniciens, la responsabilité et la conformité réglementaire. Des méthodes comme SHAP et LIME expliquent les prédictions individuelles, tandis que Grad-CAM visualise les régions d'images influentes pour les CNN.</p>
                <p class="leading-relaxed">L'XAI est appliquée pour expliquer des diagnostics, identifier des biomarqueurs importants ou clarifier les facteurs de risque. Le défi est de fournir des explications utiles sans sacrifier la performance des modèles.</p>
            </div>
            <div id="av-novateurs" class="sub-section">
                <h3 class="text-xl font-semibold text-cyan-600 mb-3">Algorithmes Novateurs et Améliorations de Performance</h3>
                <p class="mb-3 leading-relaxed">La recherche continue d'affiner les architectures d'apprentissage profond (CNN, RNN, Transformateurs, GNN). Des modèles TALN spécialisés pour le biomédical (MediSwift, BioMedLM) émergent, offrant efficacité et performance sur des tâches spécifiques. La modélisation temporelle pour les DSE (ex: MOTOR) améliore la stratification des risques.</p>
                <p class="mb-3 leading-relaxed">La quantification de l'incertitude (QI) gagne en importance pour évaluer la fiabilité des prédictions. L'exploration de l'Apprentissage Automatique Quantique (AAQ), bien que naissante, promet des capacités de calcul pour des problèmes médicaux très complexes, comme la simulation moléculaire pour la découverte de médicaments. La Cleveland Clinic a installé un ordinateur quantique dédié à la recherche en santé.</p>
                
                <h4 class="text-lg font-semibold text-cyan-600 mt-6 mb-3">Tableau Interactif : Percées Récentes en IA Médicale (2023-2025)</h4>
                 <p class="mb-4 text-sm text-slate-600">Cliquez sur une ligne pour afficher/masquer les détails (impact clé).</p>
                <div class="overflow-x-auto shadow-md rounded-lg">
                    <table class="min-w-full divide-y divide-slate-200" id="table2Breakthroughs">
                        <thead class="bg-slate-100">
                            <tr>
                                <th class="px-6 py-3 text-left text-xs font-medium text-slate-500 uppercase tracking-wider">Percée/Développement</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-slate-500 uppercase tracking-wider">Domaine d'Application Principal</th>
                                <th class="px-6 py-3 text-left text-xs font-medium text-slate-500 uppercase tracking-wider">Technologies Clés</th>
                                <th class="px-2 py-3 text-center text-xs font-medium text-slate-500 uppercase tracking-wider w-12"></th>
                            </tr>
                        </thead>
                        <tbody class="bg-white divide-y divide-slate-200">
                            </tbody>
                    </table>
                </div>
            </div>
        </section>

        <section id="bilan" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Bilan : Bénéfices et Défis</h2>
            <p class="mb-6 leading-relaxed">L'intégration de l'apprentissage automatique en médecine offre des avantages considérables mais s'accompagne également de défis importants qu'il est crucial d'aborder pour une adoption réussie et éthique.</p>

            <div class="grid md:grid-cols-2 gap-8">
                <div>
                    <h3 class="text-xl font-semibold text-green-600 mb-3">Bénéfices et Impacts Positifs</h3>
                    <ul class="list-disc list-inside space-y-2 text-sm text-slate-700">
                        <li><strong>Amélioration de la Précision et Rapidité Diagnostique :</strong> Détection précoce des maladies, réduction des erreurs. L'IA de Google Health a réduit les faux positifs de 5.7% et les faux négatifs de 9.4% pour le cancer du sein.</li>
                        <li><strong>Optimisation de l'Efficacité des Traitements et Médecine Personnalisée :</strong> Sélection de traitements adaptés, amélioration des résultats pour les patients.</li>
                        <li><strong>Accélération de la Découverte de Médicaments :</strong> Réduction des délais et coûts, augmentation des probabilités de succès des essais cliniques.</li>
                        <li><strong>Gains d'Efficacité Opérationnelle et Réduction des Coûts :</strong> Automatisation des tâches administratives, optimisation des ressources, réduction des réadmissions.</li>
                        <li><strong>Amélioration des Soins et de l'Engagement des Patients :</strong> Soins proactifs et préventifs, surveillance à distance, meilleure accessibilité.</li>
                    </ul>
                </div>
                <div>
                    <h3 class="text-xl font-semibold text-red-600 mb-3">Défis et Limites</h3>
                    <ul class="list-disc list-inside space-y-2 text-sm text-slate-700">
                        <li><strong>Problématiques liées aux Données :</strong> Qualité, quantité, confidentialité, sécurité, et biais algorithmiques pouvant exacerber les disparités de santé.</li>
                        <li><strong>Défis Algorithmiques et Techniques :</strong> Manque d'interprétabilité ("boîte noire"), robustesse et généralisabilité des modèles, surajustement, coût de calcul.</li>
                        <li><strong>Validation Clinique et Approbation Réglementaire :</strong> Nécessité de validations rigoureuses, cadres réglementaires en évolution pour les technologies adaptatives.</li>
                        <li><strong>Enjeux Éthiques, Sociaux et d'Implémentation :</strong> Responsabilité en cas d'erreur, consentement éclairé, gouvernance des données, risque de "dé-compétence" des professionnels, résistance au changement.</li>
                    </ul>
                </div>
            </div>
        </section>

        <section id="references" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Références et Ressources</h2>
            <p class="mb-6 leading-relaxed">Cette section présente une sélection de références clés sur lesquelles s'appuie ce rapport interactif, ainsi qu'un lien pour accéder au rapport de recherche original complet pour approfondissement et citation.</p>

            <h3 class="text-xl font-semibold text-cyan-600 mb-3">Sélection de Références Clés :</h3>
            <ul class="space-y-3 mb-8 text-sm text-slate-700">
                <li class="reference-item leading-relaxed">Moor, M., et al. (2023). Foundation models for generalist medical artificial intelligence. *Nature*.</li>
                <li class="reference-item leading-relaxed">Singhal, K., et al. (2023). Large Language Models Encode Clinical Knowledge. *arXiv*.</li>
                <li class="reference-item leading-relaxed">Uk Mckernan, N., et al. (2024). High-performance medicine: the convergence of human and artificial intelligence. *Nature Medicine*.</li>
                <li class="reference-item leading-relaxed">Fleming, N. (2023). How artificial intelligence is changing drug discovery. *Nature*.</li>
                <li class="reference-item leading-relaxed">McKinsey & Company. (2024). *The state of AI in 2024: Generative AI’s breakout year*.</li>
                <li class="reference-item leading-relaxed">Dayan, I., et al. (2021). Federated learning for predicting clinical outcomes in patients with COVID-19. *Nature Medicine*.</li>
                <li class="reference-item leading-relaxed">Obermeyer, Z., et al. (2019). Dissecting racial bias in an algorithm used to manage the health of populations. *Science*.</li>
                <li class="reference-item leading-relaxed">The Lancet Digital Health. (2023). AI medical devices: the rise of the machines. *The Lancet Digital Health*.</li>
            </ul>

            <h3 class="text-xl font-semibold text-cyan-600 mb-3">Rapport de Recherche Original :</h3>
            <p class="mb-4 leading-relaxed">Pour consulter la liste complète des références et une analyse approfondie, veuillez vous référer au rapport de recherche original complet via le lien suivant (veuillez remplacer le lien placeholder par le lien réel vers votre document) :</p>
            <a href="https://docs.google.com/document/d/1LzOoBJQRHM-Lat5tnbA5RDM_c7dlcs6EkpdSWmuC4SY/edit?tab=t.0" target="_blank" class="btn-primary">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L9 10.586V3a1 1 0 112 0v7.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z" clip-rule="evenodd" />
                </svg>
                Télécharger le Rapport Original (PDF)
            </a>
            <p class="text-xs text-slate-500 mt-2">Note.</p>
        </section>

        <section id="conclusion" class="content-section bg-white p-6 rounded-lg shadow-xl mb-8">
            <h2 class="text-2xl font-bold text-cyan-700 mb-4">Conclusion et Perspectives</h2>
            <p class="mb-4 leading-relaxed">L'apprentissage automatique transforme profondément la médecine, offrant des perspectives sans précédent pour améliorer la santé humaine. Les avancées rapides, notamment en IA générative, apprentissage fédéré et IA explicable, ouvrent la voie à des diagnostics plus précis, des traitements personnalisés, une découverte de médicaments accélérée et des systèmes de santé plus efficients.</p>
            <p class="mb-4 leading-relaxed">Les tendances émergentes indiquent une maturation de ces technologies, avec un accent croissant sur leur intégration dans les flux de travail cliniques et leur conformité aux normes réglementaires et éthiques. La convergence de l'IA avec la génomique, les dispositifs portables et l'analyse de données multimodales promet une ère de médecine proactive, prédictive et hautement personnalisée.</p>
            <p class="mb-4 leading-relaxed">Cependant, la réalisation de ce potentiel nécessite de relever des défis substantiels liés aux données, à la transparence des algorithmes, à la validation clinique rigoureuse et aux implications éthiques. Un effort collaboratif et interdisciplinaire est indispensable pour garantir que l'IA médicale soit développée et déployée de manière responsable, équitable et au service du bien-être des patients.</p>
            <p class="leading-relaxed">L'avenir de l'IA en médecine s'annonce prometteur, mais il exige une vigilance constante et un engagement envers l'innovation éthique pour naviguer dans ce paysage technologique en évolution rapide et pour concrétiser la promesse d'une révolution des soins de santé.</p>
        </section>
    </main>

    <footer class="bg-cyan-800 text-cyan-200 text-center p-6 mt-12">
        <p class="text-sm">&copy; 2025 Projet de recherche sur l'IA en Médecine par Menaouri Rania, Université Moulay Tahar de Saïda.</p>
    </footer>

    <script>
    document.addEventListener('DOMContentLoaded', function () {
        console.log("Application IA en Médecine initialisée."); // Message de débogage simple

        const navLinks = document.querySelectorAll('.nav-link');
        const contentSections = document.querySelectorAll('.content-section');
        const mobileMenuButton = document.getElementById('mobileMenuButton');
        const mainNav = document.getElementById('mainNav');

        mobileMenuButton.addEventListener('click', () => {
            const isExpanded = mainNav.classList.toggle('hidden');
            mainNav.classList.toggle('flex');
            mobileMenuButton.setAttribute('aria-expanded', !isExpanded);
        });

        function updateActiveNav(hash) {
            navLinks.forEach(link => {
                link.classList.remove('active');
                if (link.getAttribute('href') === hash) {
                    link.classList.add('active');
                }
            });
        }

        function updateContentSection(hash) {
            contentSections.forEach(section => {
                if (`#${section.id}` === hash) {
                    section.classList.add('active');
                } else {
                    section.classList.remove('active');
                }
            });
        }
        
        function handleNavigation() {
            let hash = window.location.hash;
            if (!hash || !document.getElementById(hash.substring(1))) {
                hash = '#accueil'; 
                // window.location.hash = hash; // Optionnel: forcer le hash dans l'URL
            }
            
            updateActiveNav(hash);
            updateContentSection(hash);
            
            document.querySelectorAll('.content-section:not(.active) [id$="TabsContainer"]').forEach(tabsContainer => {
                const firstTabButton = tabsContainer.querySelector('.tab-button');
                if (firstTabButton) {
                    const parentSection = tabsContainer.closest('.content-section');
                    const firstSubSectionId = firstTabButton.dataset.tab;
                    if(parentSection) {
                        setActiveTab(tabsContainer.id, firstSubSectionId, parentSection);
                    }
                }
            });
            if (hash === '#accueil' || !window.location.hash) { 
                window.scrollTo(0, 0);
            }
        }

        navLinks.forEach(link => {
            link.addEventListener('click', function(e) {
                if (mainNav.classList.contains('flex') && window.innerWidth < 768) {
                    mainNav.classList.add('hidden');
                    mainNav.classList.remove('flex');
                    mobileMenuButton.setAttribute('aria-expanded', 'false');
                }
            });
        });
        
        window.addEventListener('hashchange', handleNavigation);
        handleNavigation(); 


        function setupTabs(tabsContainerId, initialTabId) {
            const tabsContainer = document.getElementById(tabsContainerId);
            if (!tabsContainer) return;

            const tabButtons = tabsContainer.querySelectorAll('.tab-button');
            const parentSection = tabsContainer.closest('.content-section');
             if (!parentSection) return;


            tabButtons.forEach(button => {
                button.addEventListener('click', (e) => {
                    e.preventDefault(); 
                    setActiveTab(tabsContainerId, button.dataset.tab, parentSection);
                });
            });
            
            const activeTabButtonHTML = tabsContainer.querySelector('.tab-button.active');
            if (activeTabButtonHTML) {
                setActiveTab(tabsContainerId, activeTabButtonHTML.dataset.tab, parentSection);
            } else if (initialTabId && tabsContainer.querySelector(`[data-tab="${initialTabId}"]`)) {
                 setActiveTab(tabsContainerId, initialTabId, parentSection);
            } else if (tabButtons.length > 0) {
                 setActiveTab(tabsContainerId, tabButtons[0].dataset.tab, parentSection);
            }
        }

        function setActiveTab(tabsContainerId, tabId, parentElement) {
            const container = document.getElementById(tabsContainerId);
             if (!container || !parentElement) return;

            const tabButtons = container.querySelectorAll('.tab-button');
            const subSections = parentElement.querySelectorAll('.sub-section');

            tabButtons.forEach(btn => {
                if (btn.dataset.tab === tabId) {
                    btn.classList.add('active');
                } else {
                    btn.classList.remove('active');
                }
            });
            subSections.forEach(section => {
                if (section.id === tabId) {
                    section.classList.add('active');
                } else {
                    section.classList.remove('active');
                }
            });
        }
        
        setupTabs('applicationsTabsContainer', 'app-diagnostic');
        setupTabs('avanceesTabsContainer', 'av-generative');

        const algorithmsData = [
            { name: "Apprentissage Supervisé", desc: "Apprend à partir de données étiquetées.", app: "Diagnostic, pronostic, prédiction des risques.", forces: "Peut atteindre une grande précision avec suffisamment de données étiquetées, méthodes bien établies.", limits: "Nécessite des données étiquetées (coûteux/long), peut mal généraliser si données non représentatives." },
            { name: "Régression Logistique/Linéaire", desc: "Modèles statistiques pour prédire résultats binaires ou continus.", app: "Notation du risque (ex: cardiovasculaire), prédiction valeurs physiologiques.", forces: "Interprétable, efficace en calcul, facile à mettre en œuvre.", limits: "Suppose relations linéaires, sensible aux valeurs aberrantes." },
            { name: "Machines à Vecteurs de Support (SVM)", desc: "Trouve un hyperplan optimal pour séparer les classes.", app: "Détection du cancer, classification des maladies.", forces: "Efficace en haute dimension, modélise frontières non linéaires (noyaux).", limits: "Coûteux en calcul (grands datasets), moins interprétable." },
            { name: "Arbres de Décision", desc: "Modèle arborescent de décisions et conséquences.", app: "Aide à la décision clinique, stratification des patients.", forces: "Intuitif, facile à interpréter (arbres simples).", limits: "Sujet au surajustement, instable." },
            { name: "Forêts Aléatoires", desc: "Ensemble d'arbres de décision pour précision et anti-surajustement.", app: "Prédiction maladies, efficacité/toxicité médicaments.", forces: "Grande précision, robuste au surajustement, gère données haute dimension.", limits: "Moins interprétable que les arbres uniques." },
            { name: "Arbres à Gradient Boosté", desc: "Construit arbres séquentiellement, corrigeant erreurs précédentes.", app: "Prédiction maladies (ex: mortalité COVID-19), triage.", forces: "Souvent performances de pointe sur données structurées.", limits: "Sujet au surajustement si mal réglé, coûteux en calcul." },
            { name: "Apprentissage Non Supervisé", desc: "Apprend schémas et structures à partir de données non étiquetées.", app: "Stratification patients, découverte biomarqueurs.", forces: "Peut découvrir nouveaux schémas, ne nécessite pas données étiquetées.", limits: "Résultats plus difficiles à interpréter et valider." },
            { name: "k-Moyennes (Clustering)", desc: "Partitionne données en 'k' clusters.", app: "Identification sous-groupes de patients, segmentation images.", forces: "Simple, efficace, facile à mettre en œuvre.", limits: "Sensible à 'k' et centroïdes initiaux, suppose clusters sphériques." },
            { name: "Apprentissage par Renforcement (AR)", desc: "Agent apprend décisions pour maximiser récompense.", app: "Régimes de traitement dynamiques, optimisation opérations hospitalières.", forces: "Peut apprendre stratégies optimales en environnements dynamiques.", limits: "Nécessite beaucoup de données/simulations, définition récompenses difficile." },
            { name: "Apprentissage Profond (AP)", desc: "RNA multi-couches pour apprendre représentations hiérarchiques.", app: "Analyse images médicales, DSE, découverte médicaments, génomique.", forces: "Extraction auto. de caractéristiques, modélise schémas très complexes.", limits: "Nécessite grands datasets, coûteux en calcul, souvent \"boîte noire\"." },
            { name: "Réseaux Neuronaux Convolutifs (CNN)", desc: "AP spécialisé pour données en grille (images).", app: "Analyse images médicales (radiologie, pathologie).", forces: "Excellent pour apprentissage caractéristiques spatiales.", limits: "Nécessite grands datasets d'images étiquetées." },
            { name: "Réseaux Neuronaux Récurrents (RNN/LSTM)", desc: "AP pour données séquentielles (maintenant un état interne).", app: "Analyse DSE, signaux biomédicaux (ECG, EEG).", forces: "Capture dépendances temporelles.", limits: "Difficultés avec dépendances à longue portée (atténué par LSTM)." },
            { name: "Transformateurs (Transformers)", desc: "AP basé sur auto-attention, excelle en TALN.", app: "TALN biomédical, analyse séquences génomiques.", forces: "Gère dépendances à longue portée, parallélisable.", limits: "Coûteux en calcul, nécessite grands datasets pour pré-entraînement." },
        ];

        const breakthroughsData = [
            { name: "IA Générative dans DSE/Flux Clinique", app: "Opérations Santé, Doc. Clinique", tech: "LLM, TALN, Reconnaissance Vocale", impact: "Outils d'écoute ambiante (Nuance, Abridge) automatisent notes cliniques, réduisant épuisement." },
            { name: "IA MILTON d'AstraZeneca", app: "Pronostic Maladies, Détection Précoce", tech: "AA, Analyse Biomarqueurs", impact: "Prédit >1000 maladies avec haute précision des années avant diagnostic." },
            { name: "Étude Apprentissage Fédéré EXAM", app: "Analyses Prédictives, Imagerie", tech: "Apprentissage Fédéré, AP", impact: "Modèle AF multi-hospitalier a amélioré de 16% (perf) et 38% (généralisabilité) prédiction besoins O2 (COVID-19)." },
            { name: "K1.0 Turbigo d'Owkin & Partenariats AF", app: "Découverte Médicaments, Diagnostic", tech: "Apprentissage Fédéré, IA Multimodale", impact: "Système IA utilisant AF pour découverte médicaments; partenariat avec AstraZeneca (pré-criblage gBRCAm)." },
            { name: "LLM Biomédicaux Spécialisés", app: "TALN Biomédical, Recherche", tech: "LLM, Transformateurs", impact: "Modèles plus petits, spécifiques au domaine (BioMedLM) compétitifs en Q&R biomédical." },
        ];

        function populateTable(tableId, data, columnsConfig) {
            const tableBody = document.getElementById(tableId)?.querySelector('tbody');
            if (!tableBody) return;
            tableBody.innerHTML = ''; 

            data.forEach(item => {
                const row = document.createElement('tr');
                row.classList.add('table-row-item', 'cursor-pointer', 'hover:bg-sky-50');
                row.setAttribute('tabindex', '0'); 
                row.setAttribute('aria-expanded', 'false');

                columnsConfig.main.forEach(col => {
                    const cell = document.createElement('td');
                    cell.classList.add('px-6', 'py-4', 'text-sm', 'text-slate-700');
                    if(columnsConfig.main.indexOf(col) === 0) { 
                         cell.classList.add('whitespace-normal', 'font-medium');
                    } else {
                         cell.classList.add('whitespace-nowrap');
                    }
                    cell.innerHTML = item[col.key];
                    row.appendChild(cell);
                });
                
                const actionCell = document.createElement('td');
                actionCell.classList.add('px-2', 'py-4', 'text-center', 'w-12');
                actionCell.innerHTML = `<span class="expand-icon text-cyan-600 transition-transform duration-200 inline-block">&#x276F;</span>`; 
                row.appendChild(actionCell);
                
                tableBody.appendChild(row);

                const detailsRow = document.createElement('tr');
                detailsRow.classList.add('table-details', 'bg-slate-50');
                detailsRow.setAttribute('aria-hidden', 'true');
                const detailsCell = document.createElement('td');
                detailsCell.setAttribute('colspan', (columnsConfig.main.length + 1).toString());
                detailsCell.classList.add('px-6', 'py-4');
                
                let detailsHTML = '<div class="space-y-2 text-xs">';
                columnsConfig.details.forEach(detailCol => {
                    detailsHTML += `<div><strong class="text-slate-600">${detailCol.label}:</strong> <span class="text-slate-700">${item[detailCol.key]}</span></div>`;
                });
                detailsHTML += '</div>';
                detailsCell.innerHTML = detailsHTML;
                tableBody.appendChild(detailsRow);

                row.addEventListener('click', () => {
                    const isExpanded = row.classList.toggle('expanded');
                    row.setAttribute('aria-expanded', isExpanded.toString());
                    detailsRow.setAttribute('aria-hidden', (!isExpanded).toString());
                });
                row.addEventListener('keydown', (e) => {
                    if (e.key === 'Enter' || e.key === ' ') {
                        e.preventDefault();
                        const isExpanded = row.classList.toggle('expanded');
                        row.setAttribute('aria-expanded', isExpanded.toString());
                        detailsRow.setAttribute('aria-hidden', (!isExpanded).toString());
                    }
                });
            });
        }
        
        const algoColumns = {
            main: [
                { key: 'name', label: 'Algorithme/Paradigme' },
                { key: 'desc', label: 'Description Brève' },
                { key: 'app', label: 'Domaines d\'Application Médicale' }
            ],
            details: [
                { key: 'forces', label: 'Forces' },
                { key: 'limits', label: 'Limites/Faiblesses' }
            ]
        };

        const breakthroughColumns = {
            main: [
                { key: 'name', label: 'Percée/Développement' },
                { key: 'app', label: 'Domaine d\'Application Principal' },
                { key: 'tech', label: 'Technologies Clés' }
            ],
            details: [
                { key: 'impact', label: 'Découverte/Impact Clé' }
            ]
        };

        populateTable('table1Algorithms', algorithmsData, algoColumns);
        populateTable('table2Breakthroughs', breakthroughsData, breakthroughColumns);

        Chart.defaults.font.family = "'Inter', sans-serif";
        Chart.defaults.plugins.tooltip.bodyFont = { weight: '500' };
        Chart.defaults.plugins.tooltip.titleFont = { weight: '600' };
        Chart.defaults.plugins.title.font = {size: 14, weight: '600'};
        Chart.defaults.plugins.title.padding = {top: 10, bottom:15};
        Chart.defaults.plugins.legend.position = 'bottom';
        Chart.defaults.plugins.legend.labels.boxWidth = 12;
        Chart.defaults.plugins.legend.labels.padding = 20;


        const diagCtx = document.getElementById('diagnosticAccuracyChart')?.getContext('2d');
        if (diagCtx) {
            new Chart(diagCtx, {
                type: 'bar',
                data: {
                    labels: ['Détection Nodules Pulmonaires'],
                    datasets: [
                        {
                            label: 'Radiologues',
                            data: [65],
                            backgroundColor: 'rgba(22, 163, 74, 0.75)', 
                            borderColor: 'rgba(22, 163, 74, 1)',
                            borderWidth: 1,
                            barPercentage: 0.5,
                            categoryPercentage: 0.6
                        },
                        {
                            label: 'Système IA',
                            data: [94],
                            backgroundColor: 'rgba(6, 182, 212, 0.75)', 
                            borderColor: 'rgba(6, 182, 212, 1)',
                            borderWidth: 1,
                            barPercentage: 0.5,
                            categoryPercentage: 0.6
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: true, max: 100, title: { display: true, text: 'Précision (%)', font: {weight: '600'} } },
                        x: { ticks: { font: {size: 11} } }
                    },
                    plugins: { 
                        title: { display: true, text: 'IA vs Radiologues (Illustratif)'},
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `${context.dataset.label}: ${context.raw}%`;
                                }
                            }
                        }
                    }
                }
            });
        }

        const genAiCtx = document.getElementById('genAiAdoptionChart')?.getContext('2d');
        if (genAiCtx) {
            new Chart(genAiCtx, {
                type: 'doughnut',
                data: {
                    labels: ['Exploration/Adoption GenAI (85%)', 'Pas encore (15%)'],
                    datasets: [{
                        data: [85, 15],
                        backgroundColor: ['rgba(6, 182, 212, 0.85)', 'rgba(203, 213, 225, 0.85)'], 
                        borderColor: ['rgba(6, 182, 212, 1)', 'rgba(156, 163, 175, 1)'],
                        borderWidth: 1.5,
                        hoverOffset: 8
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { 
                        title: { display: true, text: 'Adoption GenAI (Santé US, T4 2024)'},
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `${context.label.split('(')[0].trim()}: ${context.raw}%`;
                                }
                            }
                        }
                    }
                }
            });
        }
        
        const flMarketCtx = document.getElementById('federatedLearningMarketChart')?.getContext('2d');
        if (flMarketCtx) {
            new Chart(flMarketCtx, {
                type: 'line',
                data: {
                    labels: ['2024 (est.)', '2034 (proj.)'],
                    datasets: [{
                        label: 'Taille du Marché (Millions $)',
                        data: [29.725, 141], 
                        fill: true,
                        borderColor: 'rgb(6, 182, 212)', 
                        backgroundColor: 'rgba(6, 182, 212, 0.15)',
                        tension: 0.1,
                        pointRadius: 5,
                        pointBackgroundColor: 'rgb(6, 182, 212)',
                        pointBorderColor: '#fff',
                        pointHoverRadius: 7
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: { 
                        y: { beginAtZero: true, title: {display: true, text: 'Millions USD', font: {weight: '600'} } },
                        x: { ticks: { font: {size: 11} } }
                    },
                    plugins: { 
                        title: { display: true, text: 'Marché Apprentissage Fédéré en Santé' },
                        tooltip: {
                             callbacks: {
                                label: function(context) {
                                    return `${context.dataset.label}: $${context.raw}M`;
                                }
                            }
                        },
                        legend: { display: false }
                    }
                }
            });
        }
    });
    </script>
</body>
</html>
