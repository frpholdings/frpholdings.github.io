---
layout: default
title: Most Recent Filings
order: 20
---

{% assign filings = site.static_files | where_exp: "f","f.path contains 'filings'" %}
{% assign _filings = '' | split: ',' %}
{% for filing in filings %}
    {% assign _fs = filing.name | split: '-' %}
    {% capture formatted_filing_date %}{{ _fs[1] | slice: 0,4 }}-{{ _fs[1] | slice: 4,2 }}-{{ _fs[1] | slice: 6,2 }}{% endcapture %}
    {% assign formatted_filing_date = formatted_filing_date | date: "%B %d, %Y" %}
    {% assign dc = _fs[2] | split: '.' %}
    {% case dc[0] %}
        {% when '10q' %}
            {% assign filing_document_type = '10-Q' %}
        {% when '10k' %}
            {% assign filing_document_type = '10-K' %}
        {% when 'ar' %}
            {% assign filing_document_type = 'Annual Report' %}
        {% else %}
            {% assign filing_document_type = _fs[2] %}
    {% endcase %}
    {% assign filing_details = '' | split: ',' | push: _fs[1] | push: _fs[2] | push: formatted_filing_date | push: filing_document_type | push: filing %}
    {% assign _filings = _filings | push: filing_details %}
{% endfor %}

{% assign fs = site.static_files | where_exp: "f","f.path contains 'xbrl'" %}
{% assign ds = '' | split: ',' %}
{% for f in fs %}
    {% assign d = f.name | slice: 5,8 %}
    {% assign ds = ds | push: d %}
{% endfor %}
{% assign ds = ds | uniq | sort %}
{% assign _ds = '' | split: ',' %}

{% for d in ds reversed %}
    {% capture fd %}{{ d | slice: 0,4 }}-{{ d | slice: 4,2 }}-{{ d | slice: 6,2 }}{% endcapture %}
    {% assign fd = fd | date: "%B %d, %Y" %}
    {% assign _fs = fs | where_exp: "f","f.path contains d" %}
    {% assign _f = '' | split: ',' | push: fd | push: _fs %}
    {% assign _ds = _ds | push: _f %}
{% endfor %}

<style>
ul.sideways { padding: 0; list-style-type: none; }
ul.sideways li { display: inline; white-space: pre; }
ul.sideways li a {font-weight: bold; text-decoration: underline;text-transform: lowercase; }
ul.sideways li:after { content: "  \00b7  "; }
ul.sideways li:last-child:after { content: ""; }
</style>

### {{page.title}}

{% for f in _filings reversed %}
- **[{{f[3]}} Document ({{f[2]}}) PDF]({{ f[4].path }})**
{% endfor %}

<br />
#### XBRL Files

{% for d in _ds %}
<div style="line-height: 1.6;"> 
    <strong>{{ d[0] }}</strong> 
</div> 
<div style="line-height: 1.6;">
<ul class="sideways">
    {% for f in d[1] %}
    <li><a href="{{ f.path }}">{{ f.name }}</a></li>
    {% endfor %}
</ul>
</div>
{% endfor %}