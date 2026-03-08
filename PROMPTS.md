AI Prompts Used During Development:

1.Debugging Tool Execution
Prompt:

"Why does my RAWG API search return no results when searching for RPG games?"

This led to discovering that the RAWG API requires specific genre slugs such as:

role-playing-games-rpg

instead of simply "rpg".

A genre mapping system was then implemented.


2.Improving Natural Language Queries
Prompt:

"How can I make the AI agent understand genres like 'sci-fi', 'open world', and 'exploration' when the RAWG API does not support those genres?"

This resulted in implementing a genre translation map to convert natural user language into supported API genres.