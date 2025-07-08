---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

Bonjour, Hello, مرحبا

Je suis Mohammed Sbihi , étudiant en 5ᵉ année d’ingénierie des Télécommunications et Réseaux à l’ENSAF de Fès , avec une spécialisation en cybersécurité et en développement web full-stack .

Passionné par les technologies modernes, je me forme et développe des applications sécurisées, performantes et évolutives , en utilisant des stacks comme Next.js, Node.js, TypeScript, Supabase , ainsi que Flutter pour les solutions mobiles.

Mon objectif ? Allier sécurité informatique et développement logiciel pour créer des systèmes robustes et fiables, tout en explorant les nouvelles tendances du web , de l’IA et de la cybersécurité .

# Skills
   
{% assign link = "https://cdn.jsdelivr.net/gh/devicons/devicon/icons/" %}
{% assign endlink = "-original.svg" %}
{% assign skills = "
FrontEnd>
    HTML=/images/about/html-logo.png,
    CSS=/images/about/css-logo.png,
    Dart=" | append: link | append: "dart/dart-original.svg,    
    JavaScript=/images/about/js-logo.png,
    Next.js=" | append: link | append: "nextjs/nextjs-original.svg;
BackEnd>
    Python=" | append: link | append: "python/python-original.svg,
    PHP=" | append: link | append: "php/php-original.svg,
    Laravel=" | append: link | append: "laravel/laravel-original.svg,
    Firebase=" | append: link | append: "firebase/firebase-plain.svg;
Design>
    Figma=" | append: link | append: "figma/figma-original.svg,
    XML=/images/about/xml-logo.png;
Autres>
    C=" | append: link | append: "c/c-original.svg,
    C++=" | append: link | append: "cplusplus/cplusplus-original.svg,
    MATLAB=" | append: link | append: "matlab/matlab-original.svg,
    LabVIEW=" | append: link | append: "labview/labview-original.svg,
    Arduino=" | append: link | append: "arduino/arduino-original.svg;
Base de données>
    MySQL=" | append: link | append: "mysql/mysql-original.svg,
    Oracle=" | append: link | append: "oracle/oracle-original.svg;

" %}
   
{% include skills.html  %}

{% include style.scss %}

<!-- {% assign skills = "FrontEnd:HTML:/images/about/html-logo.png,FrontEnd:CSS:/images/about/css-logo.png,FrontEnd:JavaScript:/images/about/js-logo.png,FrontEnd:Dart:/images/about/dart-logo.png,BackEnd:Python:/images/about/python-logo.png,BackEnd:PHP:/images/about/php-logo.png,BackEnd:Laravel:/images/about/laravel-logo.png,BackEnd:Firebase:/images/about/firebase-logo.png,Design:Figma:/images/about/figma-logo.png,Design:XML:/images/about/xml-logo.png," | split: "," %}

{% assign categories = "FrontEnd,BackEnd,Design,Autres" | split: "," %} -->


