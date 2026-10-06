# Regex Patterns
pattern_words_5 = r'\b[A-Za-z]{6,}\b'
pattern_numbers = r'\b\d+(?:\.\d+)+\b|\b\d+\b'
pattern_capitals = r'\b[A-Z][a-zA-Z]*\b'
pattern_vowels = r'\b[aeiouAEIOU][a-zA-Z]*\b'

pattern_email = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b'
pattern_url = r'https?://[^\s]+|www\.[^\s]+'
pattern_phone = r'\(?\+?\d{1,3}\)?[-.\s]?\d{3}[-.\s]?\d{4}|\b\d{3}-\d{4}\b'

print("--- TASK 2: TEST_LINES Extraction & Replacement Test ---")
for i, line in enumerate(TEST_LINES, 1):
    print(f"\n[Test Line {i}]: {line}")
    print("  > Words > 5 letters:", re.findall(pattern_words_5, line))
    print("  > Numbers/Decimals/Versions:", re.findall(pattern_numbers, line))
    print("  > Capitalized Words:", re.findall(pattern_capitals, line))
    print("  > Vowel-starting Words:", re.findall(pattern_vowels, line))
    
    # Substitutions
    sub_line = re.sub(pattern_email, '<EMAIL>', line)
    sub_line = re.sub(pattern_url, '<URL>', sub_line)
    sub_line = re.sub(pattern_phone, '<PHONE>', sub_line)
    print("  > Substituted Line:", sub_line)


    // task 3
    # RegexpTokenizer pattern preserving contractions, hyphens, decimals, versions, emails, URLs, phones
custom_pattern = r'''(?x)
    \b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b  # Emails
    | https?://[^\s]+|www\.[^\s]+                       # URLs
    | \(?\+?\d{1,3}\)?[-.\s]?\d{3}[-.\s]?\d{4}           # Phone numbers
    | \b\d+(?:\.\d+)+\b                                 # Decimals & Version numbers
    | \b\w+(?:-\w+)+\b                                  # Hyphenated words
    | \b\w+(?:'\w+)?\b                                  # Words with contractions
    | [^\w\s]                                           # Remaining punctuation
'''

custom_tokenizer = RegexpTokenizer(custom_pattern)

def my_tokenize(text):
    return custom_tokenizer.tokenize(text)

print("--- TASK 3: Tokenizer Comparison on TEST_LINES ---")
for line in TEST_LINES:
    print("\nOriginal Text:", line)
    print("word_tokenize(): ", word_tokenize(line))
    print("my_tokenize()  : ", my_tokenize(line))



    # Extract unique final tokens across all tickets
all_final_tokens = set()
for res in processed_data:
    all_final_tokens.update(res['final_tokens'])

sorted_final_tokens = sorted(list(all_final_tokens))

# Initialize Stemmers & Lemmatizer
p_stemmer = PorterStemmer()
l_stemmer = LancasterStemmer()
w_lemmatizer = WordNetLemmatizer()

# Target suffix: 'ing'
r_stemmer = RegexpStemmer('ing$', min=4)

def get_wordnet_pos(treebank_tag):
    if treebank_tag.startswith('J'):
        return wordnet.ADJ
    elif treebank_tag.startswith('V'):
        return wordnet.VERB
    elif treebank_tag.startswith('N'):
        return wordnet.NOUN
    elif treebank_tag.startswith('R'):
        return wordnet.ADV
    else:
        return wordnet.NOUN

# Get POS tags
pos_tags = dict(nltk.pos_tag(sorted_final_tokens))

comparison_rows = []
for word in sorted_final_tokens:
    p_out = p_stemmer.stem(word)
    l_out = l_stemmer.stem(word)
    r_out = r_stemmer.stem(word)
    
    wn_pos = get_wordnet_pos(pos_tags.get(word, 'N'))
    lem_out = w_lemmatizer.lemmatize(word, pos=wn_pos)
    
    # Include only tokens where outputs differ across methods
    if len({p_out, l_out, r_out, lem_out}) > 1:
        comparison_rows.append({
            'Original Word': word,
            'POS Tag': pos_tags.get(word, ''),
            'Porter': p_out,
            'Lancaster': l_out,
            'RegexpStemmer': r_out,
            'WordNet Lemmatizer': lem_out
        })

df_t4 = pd.DataFrame(comparison_rows)
print("--- TASK 4: Stemming vs Lemmatization Comparison Table ---")
display(df_t4)
    
