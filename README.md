[Uploading README (1).md…]()
# Re-testing Google Antigravity CLI (agy) via the official route: background and smoke-test results

日本語版: [README.ja.md](README.ja.md)

Our AI team had banned the use of Google Antigravity's CLI (`agy`). The reason was the risk that driving it through unofficial wrappers could get the Google account suspended. That premise has changed: `agy` is now Google's own official CLI, and scheduled execution is offered as an official product feature. This document records why we banned it, what changed, and the results of the minimal smoke test we ran on 2026-11-27, limited to facts we actually verified.

## Background: why we had banned agy

Our team had formally decided to ban the use of `agy`. At the time, automating `agy` through external mechanisms could not clearly be called an officially intended route, and we saw no reason to accept the risk of an account suspension. The account is the foundation of the work, so we judged that caution was reasonable.

Accordingly, our use of Gemini was restricted to requests through the officially provided Gemini API only.

This change of direction is not because the earlier decision was wrong. The premise itself changed.

## What changed

1. **Antigravity 2.0 and the official CLI**
   With Antigravity 2.0, announced at Google I/O in May 2026, background automation via scheduled execution became a formal product feature. `agy` itself is a CLI provided by Google, so there is no longer a need to rely on unofficial wrappers.

2. **"Tasks" in the Gemini app**
   The Gemini app's "Tasks", launched on 2026-11-17 (Japan included), runs tasks in a remote browser on Google's side. In other words, an official home for running routine work on a schedule now exists on Google's side as well.

## The smoke test we ran (2026-11-27)

Before changing our policy, we ran a minimal check. Only the following was performed:

- **Installer inspection**
  We read the official install script from antigravity.google in full before running it, and confirmed that it verifies the downloaded binary against a SHA512 checksum from the official manifest.
- **Version**
  The installed `agy` was version 1.5.18.
- **Authentication**
  No personal Google account login was used at all. We connected in a headless configuration using an API key, specifying `modelProvider` as `gemini` following the official procedure. Setting only the `GEMINI_API_KEY` environment variable was not enough; the model provider setting was also required.
- **Execution test**
  In an empty directory, we ran one simple prompt in print mode. The response came back exactly as instructed, and no quota errors occurred.
- **Scheduling**
  The `/schedule` slash command exists in this build and works (it responds asking what to schedule, including cron expressions). `/automation` is not in this CLI build; it is a feature of the desktop app.

## Reproduction sketch

Generic outline only, with placeholders:

```bash
# 1. Get the official installer (read it in full before running)
curl -fsSL https://antigravity.google/cli/install.sh -o install.sh
less install.sh   # inspect first
bash install.sh

# 2. Headless authentication via API key
export GEMINI_API_KEY="<your-api-key>"

# 3. Run a trivial prompt
agy --settings '{"modelProvider": "gemini"}' --print "Reply with exactly: OK"
```

## Caveats

To be clear, this is only a successful smoke test.

- Where `agy` will run full-time (a local PC or a cloud VM) has not been decided yet.
- The formal revision of the ban within our team is still to come; we will decide based on these results.
- This is not full production use; adoption will start with small tasks, step by step.

## Conclusion

Within the official route, we confirmed that `agy` 1.5.18 installs from the official installer, authenticates headlessly with an API key, executes a prompt, and provides a working `/schedule` command. Combined with the Gemini app's Tasks, this gives us a workable outlook for using both in practice.

We banned it once; the situation changed, so we tested it again. This document is that record.
