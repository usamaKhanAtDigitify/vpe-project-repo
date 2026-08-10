# VPE Platform Docs

Documentation for the VPE project — infrastructure, cost, and operations.

## Contents

- [Cost Optimization Plan](costing.md) — current spend, optimization recommendations, and ownership across NonProd/QA and Production.

## About this site

This site is built with MkDocs and deployed to ECS Fargate. Changes merged to `main` are built into a container image, pushed to ECR, and rolled out automatically.

To preview locally:

    pip install -r requirements.txt
    mkdocs serve