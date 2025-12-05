https://www.runcomfy.com/tutorials/

IC-Light(Imposing Consistent Light).
Remember those days when we used to play with multiple masking effects and settings in Photoshop to get the desired results? Now, it can be done with a single click.
# Models
iclight_sd15_fc.safetensors: The default relighting model, conditioned on text and foreground of the inputted image. You can use initial latent to influenece the relighting effect.  
iclight_sd15_fcon.safetensors: Same as "iclight_sd15_fc.safetensors" but trained with offset noise.  
iclight_sd15_fbc.safetensors: Relighting model conditioned with text, foreground, and background for the image.  
