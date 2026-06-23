Deploy interactive tasks to Cloudflare Pages and return full links to every published page.

!npx wrangler pages deploy "D:\work\interactive-tasks" --project-name=interactive-tasks

After deploying, list all .html files in D:\work\interactive-tasks and print the full URL for each one in the format:
https://interactive-tasks.pages.dev/<filename>
