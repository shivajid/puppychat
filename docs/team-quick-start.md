# Puppy Chat: teammate quick start

This guide is for the macOS team installer. You need a Google Cloud account that your administrator has granted access to the team's model connections. The model servers must already be running.

1. Install Puppy Chat from the team DMG and open it.
2. In **Connect to team models → Requirements**, follow the official installation links for any missing tools: Google Cloud CLI (`gcloud`), `kubectl`, and `gke-gcloud-auth-plugin`. Reopen the app after installation if needed, then choose **Check again**. No source checkout or Node.js installation is required.
3. In **Cloud account**, choose your existing account or select **Sign in / add another account** and complete sign-in in your browser. This is separate from Gmail/Drive sign-in. Your organization may require additional device or sign-in steps.
4. In **Connections**, choose **Connect / retry**. The app checks cloud access, opens local tunnels, checks the exact served model, and sends a small test request. Kimi must be ready for chat. Other models and browser tools are optional; affected workforce or tool features need their corresponding services.
5. Optionally enable **Connect automatically when Puppy Chat opens**. **Save startup preference** saves this choice even if a model is unavailable. The app retries dropped connections while running and checks again after the Mac wakes.
6. In **API keys**, enter provider keys or choose the JSON file your administrator shared through your team's secure channel. Import previews show provider names. Existing keys are preserved unless you explicitly select **Replace existing keys**. Choose **Save entered keys** or **Import keys** before continuing. Keys are optional for basic model chat.
7. Choose **Use team models & finish**. This applies the bundled chat, workforce, and fallback settings. It replaces any custom model assignments, so choose **Use a custom model endpoint** instead if you want to keep a different deployment.

After setup, subsequent launches open directly to connection status; choose **Open Puppy Chat** when the main model is ready. Reopen the full flow at **Settings → Team setup**. Individual keys are also available at **Settings → Cloud & API Keys**. Saved key values are hidden; paste a replacement to rotate a key, or choose **Remove** to delete the device's saved copy. Saving a key does not test the provider or revoke a copy held elsewhere.

## Troubleshooting

- **Missing tool:** use the installation link in Requirements, then check again.
- **Sign in again:** finish Google Cloud sign-in, then reconnect.
- **Access denied:** use **Copy connection report** and send it with your Google Cloud account to your administrator.
- **Different model:** your administrator should update the deployment profile to match the served model ID.
- **Model did not respond:** the tunnel may work while the model server is unhealthy or busy. Retry or contact the server administrator.
- **Port occupied:** the app never kills unrelated processes. Close the conflicting service or ask your administrator for help.
- **Keys could not be saved:** unlock your operating-system keychain and retry. A failed save preserves the previously saved values.

**Disconnect / cancel** stops only tunnels started by Puppy Chat. Existing external tunnels are left running. Quitting the app stops its tunnels. **Open workspace without connecting** lets you inspect saved work; new model requests still require a working connection.
