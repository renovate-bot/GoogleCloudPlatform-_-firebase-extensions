## See It In Action

You can test out this extension right away!

1. Go to your Cloud Firestore dashboard in the Firebase console.
2. If it doesn't exist, create the collection that you specified during installation: **${param:COLLECTION_NAME}**.
3. Add a document to this collection. The extension will expect the handlebar variables in your prompt to have values specified as fields in your document. The extension will substitute the values into the prompt before passing the prompt to the generative model.
4. In a few seconds, you'll see a status field appear in the document. This field will update as the extension processes the message. When processing is finished, the **${param:RESPONSE_FIELD}** field of the document should be populated with the response generated by Google AI.

Note: You can also use the Firebase Admin SDK to add a document:

```javascript
const ref = await admin
    .firestore()
    .collection("${param:COLLECTION_NAME}")
    .add({ ... })

ref.onSnapshot(snap => {
    if (snap.get('${param:RESPONSE_FIELD}')) console.log(
        'RESPONSE:',
        snap.get('${param:RESPONSE_FIELD}')
    )
})
```

## About the providers

The extension gives you a choice of what provider to use for the available models:

- Google AI: For more details on this Gemini API, see the [Google AI documentation](https://ai.google.dev/docs).

- Vertex AI: For more details on the Vertex AI Gemini API, see the [Vertex AI documentation](https://cloud.google.com/vertex-ai/docs/generative-ai/model-reference/gemini).

## About the models

For Google AI the list of supported models is [here](https://ai.google.dev/models/gemini), and this parameter should be set to the model name, the second segment of the model code (for
example `models/gemini-1.0-pro` should be chosen as `gemini-1.0-pro`).

For Vertex AI, the list of models is [here](https://cloud.google.com/vertex-ai/docs/generative-ai/learn/models).

#### Multimodal Prompts

Many of the Gemini models accept multimodal prompts. This extension allows for multimodal prompting with images using such models. Note that this feature is not supported for models such as `gemini-1.0-pro` which do not allow multimodal prompts.

On installation you may pick an `image` field. The image field must be the Cloud Storage URL of an object (e.g `gs://my-bucket.appspot.com/filename.png`). This image will then be provided as part of the prompt.

##### Gemini Pro Vision (deprecated)

This extension has historically supported calls to the (now deprecated) Gemini Pro Vision model on Google AI and Vertex AI APIs.

For the Gemini Pro Vision models Google AI requires prompts to have both an image and text part, whereas Vertex AI allows gemini-pro-vision to be prompted with text only as well. If you have selected to use the Gemini Pro Vision model (deprecated) and have Google AI as a provider then any document handled by the extension must contain an image field.

The Gemini Pro Vision API has a limit on image sizes. For Google AI this limit is currently 1MB, and for Vertex AI this limit is 4MB. This extension compress and resize images that fall above this limit.

## Handling errors

If the extension encounters an error, it will write an error message to the document in `status` field. You can use this field to monitor for errors in your documents. Currently some errors will instruct you to visit the Cloud Function logs for the extension, to avoid exposing sensitive information.

## Monitoring

As a best practice, you can [monitor the activity](https://firebase.google.com/docs/extensions/manage-installed-extensions#monitor) of your installed extension, including checks on its health, usage, and logs.
