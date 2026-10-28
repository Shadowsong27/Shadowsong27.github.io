# Overview

This repo contains the both the static website materials and the 
builder of my personal blog.

It uses `hugo` instead of `jekyll` now, which actually feels better.

# Build and Deploy (in case I forgot)

- always work on `builder` branch locally

- preview blogs with `hugo serve`

- commit your changes 

- run `bash deploy.sh` 

# Google Analytics

**Tracking ID**: `G-1SHS81MHHD` (Google Analytics 4)

## Verifying Tracking

To confirm that analytics tracking is working:

1. Visit https://analytics.google.com
2. Navigate to Real-Time reports
3. Check for active users

**Note**: Historical data from the deprecated Universal Analytics (GA3) tracking ID `GA-363147269` is not accessible. GA3 was sunset by Google in July 2023. Only new pageview data collected by GA4 (from March 2026 onwards) is available.

## Configuration

Analytics is configured in `config.toml`:
- `googleAnalytics = "G-1SHS81MHHD"` (legacy field for backward compatibility)
- `services.googleAnalytics.ID = "G-1SHS81MHHD"` (Hugo's native GA4 support)

The tracking script is automatically injected in production builds via a custom `layouts/partials/head.html` override that uses Hugo's built-in Google Analytics template.

# Theme - Tranquilpeak

### Credits

- **Authors**: [Louis Barranqueiro (LouisBarranqueiro)](https://github.com/LouisBarranqueiro) and [Thibaud Leprêtre (kakawait)](https://github.com/kakawait)
- **Version**: 0.4.4-BETA (based on Hexo version 1.10.0)
- **Compatibility**: Hugo v0.20.1

### Docs and Usage

https://github.com/kakawait/hugo-tranquilpeak-theme
