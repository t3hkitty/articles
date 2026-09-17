# **Gemini & NotebookLM Second-Brain Architecture & Workflow Guide**

A practical guide to custom prompt engineering, anti-hallucination grounding, native conversation syncing, and fallback export tools.

# **1\. Core System & Custom Instruction Snippets**

Drop these into Gemini's System Instructions, profile instructions, or session headers to enforce clarity and eliminate hallucinations.

## **A. High Scaffolding & Anti-Fluff (AuDHD Ergonomics)**

I want direct answers first with high structural scaffolding (bold headers, bullet points, compact tables) and no conversational filler or introductory setups. Prioritize clear scannability, concise chunks, and high contrast. Do not repeat my prompt or include transitional pleasantries.

## **B. Anti-Hallucination & Fact Grounding**

Ground all responses strictly in provided context, retrieved notes, or verified tool outputs. If a fact, technical detail, or historical conversation memory is not present or is ambiguous, explicitly state: "Information not found in provided sources." Never guess, fabricate recalls, or extrapolate unstated facts.

## **C. Screenshot OCR & Image Ingestion**

When an image or screenshot is provided:

&nbsp;

* Extract raw text accurately via OCR. Never paraphrase verbatim chat evidence during initial extraction.  
* If the screenshot is too blurry, low-resolution, or illegible to read with 100% certainty, explicitly warn that the image is too degraded before proceeding.  
* Identify the platform/app context (e.g., Slack, Zendesk, Threads). If ambiguous, ask rather than guess.  
* Format output with consecutive Pacific Time Zettelkasten timestamps (YYYYMMDD-HHMM) and relevant hashtags.

## **D. Media & Link Proxy Digest**

For shared web links, YouTube videos, or audio files:

&nbsp;

* Provide a direct, structured proxy summary (core thesis, key timestamps, dialogue breakdowns).  
* For video/audio, capture tone and visual context directly.  
* Constraint: Never instruct to "watch the video" or "read the article"—act entirely as the complete proxy digest.

# **2\. Conversation Archiving: Native "Add to Notebook" vs. Fallbacks**

## **Primary Method: Native Gemini-to-NotebookLM Sync**

The built-in integration directly moves or adds conversations into your notebook without scraping or file conversions:

&nbsp;

1. In the Gemini conversation list (left sidebar) or thread options, click the three-dot menu (⋮) next to the chat.  
2. Select **Add to notebook** (or **Move to notebook**).  
3. Choose the target notebook (e.g., "Gemini history").  
4. The entire thread is indexed directly as a semantic source.

## **Comparison Matrix**

| Feature | Native "Add to Notebook" | Manual Bookmarklet / PDF |
| :---- | :---- | :---- |
| Friction | 2 clicks | 5+ manual steps |
| Formatting | Native turn-by-turn parsing | Dependent on browser DOM |
| Searchability | Instant semantic chunks & citations | Re-indexes raw text/PDF |
| Maintenance | Zero script maintenance | Breaks if web UI changes |

# **3\. Fallback Tool: 1-Click Browser Export Bookmarklet**

For local backup or exporting directly to Markdown (.md):

## **Bookmarklet Code (JavaScript)**

Save this as a browser bookmark URL named "Export Gemini MD":(function(){

&nbsp;

&nbsp;&nbsp;const turns \= document.querySelectorAll('user-query, model-response, \[data-test-id="user-query"\], \[data-test-id="model-response"\]');

&nbsp;

&nbsp;&nbsp;let md \= \`\# Gemini Conversation Export\\nGenerated: ${new Date().toISOString()}\\n\\n---\\n\\n\`;

&nbsp;

&nbsp;&nbsp;if (turns.length \=== 0\) {

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;const allMessages \= document.querySelectorAll('.conversation-container, .query-text, .response-container');

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;allMessages.forEach(el \=\> { md \+= el.innerText.trim() \+ '\\n\\n---\\n\\n'; });

&nbsp;

&nbsp;&nbsp;} else {

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;turns.forEach(turn \=\> {

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const isUser \= turn.tagName.toLowerCase().includes('user') || turn.getAttribute('data-test-id') \=== 'user-query';

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const role \= isUser ? '\#\#\# 👤 User' : '\#\#\# 🤖 Gemini';

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;const content \= turn.innerText.trim();

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;md \+= \`${role}\\n\\n${content}\\n\\n---\\n\\n\`;

&nbsp;

&nbsp;&nbsp;&nbsp;&nbsp;});

&nbsp;

&nbsp;&nbsp;}

&nbsp;

&nbsp;&nbsp;const blob \= new Blob(\[md\], { type: 'text/markdown;charset=utf-8;' });

&nbsp;

&nbsp;&nbsp;const url \= URL.createObjectURL(blob);

&nbsp;

&nbsp;&nbsp;const a \= document.createElement('a');

&nbsp;

&nbsp;&nbsp;const now \= new Date();

&nbsp;

&nbsp;&nbsp;const pad \= n \=\> String(n).padStart(2, '0');

&nbsp;

&nbsp;&nbsp;const ts \= \`${now.getFullYear()}${pad(now.getMonth()+1)}${pad(now.getDate())}-${pad(now.getHours())}${pad(now.getMinutes())}\`;

&nbsp;

&nbsp;&nbsp;a.href \= url;

&nbsp;

&nbsp;&nbsp;a.download \= \`${ts}\_gemini\_session.md\`;

&nbsp;

&nbsp;&nbsp;document.body.appendChild(a);

&nbsp;

&nbsp;&nbsp;a.click();

&nbsp;

&nbsp;&nbsp;document.body.removeChild(a);

&nbsp;

&nbsp;&nbsp;URL.revokeObjectURL(url);

&nbsp;

})();

# **4\. NotebookLM Grounding Protocol**

1. **Vault Structure**: Keep a central repository notebook (e.g., "Gemini history") dedicated to archived chat transcripts.  
2. **Grounding Prompt Clause**: Include in prompts when discussing past specs: "Before answering questions about previous project decisions or configurations, cross-reference my history notebook sources. If no verified record exists, state it directly before making assumptions."
 
&nbsp;
