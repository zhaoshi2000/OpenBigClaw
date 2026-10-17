---
summary: "Troubleshoot images and other attachments sent to or from chat"
title: "Media and attachments"
read_when:
  - A skill produced a file but nothing was sent
  - A chat image arrived but the agent could not see its contents
---

## Media and attachments

<AccordionGroup>
  <Accordion title="The agent received my image but says it cannot see it">
    Check the active model's input capability with `openclaw models list --json`. A custom or proxied vision model must have `input: ["text", "image"]` in its `models.providers.<provider>.models[]` entry. If it is declared as text-only, OpenClaw omits native image input even when the chat channel downloaded the file successfully. See [Custom providers](/concepts/model-providers/custom-providers).

    Update the matching model entry, then check that the model list shows `text+image`. Send a fresh image to test the change; an image omitted from an earlier turn is not automatically added to a later text-only message. If the model is already image-capable, check the channel's media download logs and confirm the attachment was saved before the agent run.

  </Accordion>
  <Accordion title="My skill generated an image/PDF, but nothing was sent">
    Outbound attachments from the agent must use structured media fields such as `media`, `mediaUrl`, `path`, or `filePath`. See [OpenClaw assistant setup](/start/openclaw) and [Agent send](/tools/agent-send).

    ```bash
    openclaw message send --target +15555550123 --message "Here you go" --media /path/to/file.png
    ```

    Also check: the target channel supports outbound media and is not blocked by allowlists; the file is within the provider's size limits (images resize to a max side of 2048px); `tools.fs.workspaceOnly=true` limits local-path sends to workspace, temp/media-store, and sandbox-validated files; `tools.fs.workspaceOnly=false` (default) lets structured local media sends use host-local files the agent can already read, for media plus safe document types (images, audio, video, PDF, Office docs, and validated text documents such as Markdown/MD, TXT, JSON, YAML/YML). This is not a secret scanner - an agent-readable `secret.txt` or `config.json` can be attached when the extension and content validation match. Keep sensitive files outside agent-readable paths, or keep `tools.fs.workspaceOnly=true` for stricter local-path sends.

    See [Images](/nodes/images).

  </Accordion>
</AccordionGroup>
