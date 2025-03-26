# Darija tokenizers

Thanks to the [course](https://www.youtube.com/playlist?list=PLMSb3cZXtIfptKdr56uEdiM5pR6HDMoUX) I created about training LLMs from scratch, I decided to train a tokenizer for the Darija language. I used the [AtlaSet](https://huggingface.co/datasets/atlasia/Atlaset) dataset and open-sourced different versions of the tokenizer. The training was done locally on 10 million characters.

## Tokenizer sizes and hardware

I trained three different tokenizers with varying vocabulary sizes. Here is a summary:

| Version | Vocabulary size (*) | Training time |
|---------|----------------|--------------|
| Small   | 1,024          | 20 minutes   |
| Base    | 16,384         | 6 hours      |
| Large   | 32,768         | 13 hours |

> (*) Vocabulary size does not include special tokens.

The machine used for training has the following specifications:

- **RAM:** 16GB  
- **CPU:** 13th Gen Intel® Core™ i7-13650HX (20 cores)  
- **OS:** Ubuntu 24.04.2 LTS  

## BPE algorithm

The [minbpe](./minbpe/) folder contains scripts copied from [Andrej Karpathy's repo](https://github.com/karpathy/minbpe), as it is not available as a package. I made some modifications to these scripts. You can check the [train_tokenizer](./train_tokenizer.ipynb) notebook for usage instructions.

## Special characters

I have used these special characters during training:

```python
max_vocab_id = list(tokenizer.vocab.keys())[-1]
tokenizer.special_tokens = {
    "<|startoftext|>": max_vocab_id + 1,
    "<|separator|>": max_vocab_id + 2,
    "<|endoftext|>": max_vocab_id + 3,
    "<|unk|>": max_vocab_id + 4,
    "<|padding|>": max_vocab_id + 5
}
```

If you need to add more or modify the current ones, just load the tokenizer and update the `special_tokens` field.

## Challenges

The [AtlaSet](https://huggingface.co/datasets/atlasia/Atlaset) dataset contains around 900 million characters. Loading this much text uses all 16GB of RAM plus 4GB of swap memory. To avoid memory issues, I trained the tokenizer on just 10 million characters, hoping it would be enough to estimate the data distribution.

Training on 500 million characters was possible, but large vocabulary sizes would require days to complete. From my tests, the base and large tokenizers perform well.

## Inference

To use one of the trained tokenizers, load the model and encode text as follows:  

```python
from minbpe import RegexTokenizer

tokenizer = RegexTokenizer()
tokenizer.load(model_file="./output/base/darija_tokenizer.model")

tokens = tokenizer.encode("السلام لاباس؟")
# tokens: [261, 4001, 4905, 330, 299]
```

## Limitations

The [AtlaSet](https://huggingface.co/datasets/atlasia/Atlaset) dataset mainly contains text in Darija but also includes some examples in Latin letters. The tokenizer can process these cases but may not perform well on them.

## Citations

This resource is open-source. Feel free to use it, but please cite this repository in your work.

## Contact

You can reach out to me via:

- **Discord** - Username: imad_saddik  
- **LinkedIn** – [Connect with me](https://www.linkedin.com/in/imadsaddik/)  
- **Email** – [simad3647@gmail.com](mailto:simad3647@gmail.com)  

Enjoy!
