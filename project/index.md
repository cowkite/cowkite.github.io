---
layout: default
work: true
main: true
title: Projects
description: 개발에 참여한 프로젝트
---

<div class="project">
    {% for project in site.data.project-list %}
        <div class="project-info transition">
            {% if project.thumbnail != nil %}
                <img class="project-img" src="{{ project.thumbnail }}" />
            {% endif %}
            <div class="project-organization">{{ project.organization }}</div>
            <div><h1 class="project-title">{{ project.title }}</h1></div>
            <div class="project-description">{{ project.description }}</div>
            <div class="project-date">{{ project.period }}</div>
            <ul class="project-role">
                {% for role in project.role %}
                    <li>- {{ role }}</li>
                {% endfor %}
            </ul>
            <span class="project-language radius">
                {% for language in project.language %}
                    <div>{{ language }}</div>
                {% endfor %}
            </span>
            <span class="project-skill radius">
                {% for skill in project.skill %}
                    <div>{{ skill }}</div>
                {% endfor %}
            </span>
        </div>
    {% endfor %}
</div>