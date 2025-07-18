<!-- Generated by SpecStory -->

# Using Gemini Pro for Prompt Optimization (2025-06-05 10:30:27)

_**User**_

Is this possible to use Gemini Pro 2.5 as the prompt optimization agent to finetune the attached prompt to be used in Gemma3:1b to markup citation string with xml tags as wished result then use local Gemma3:1b as another agent for verification?

---

_**Assistant**_

Yes, absolutely! This is a very practical approach for prompt optimization. Based on your existing setup with `ollama_manager` and the attached prompt configuration, here's how to implement this workflow:

````python
import json
import google.generativeai as genai
import requests
from typing import List, Dict, Tuple
import time

class PromptOptimizer:
    def __init__(self, gemini_api_key: str, ollama_manager):
        genai.configure(api_key=gemini_api_key)
        self.gemini_model = genai.GenerativeModel('gemini-2.0-flash-exp')  # Using latest available
        self.ollama_manager = ollama_manager
        
    def load_prompt_config(self, config_path: str = 'ref_markup_optV2.json') -> Dict:
        """Load the current prompt configuration"""
        with open(config_path, 'r', encoding='utf-8') as f:
            return json.load(f)[0]
    
    def optimize_prompt_with_gemini(self, current_prompt: Dict, 
                                   performance_issues: List[str]) -> str:
        """Use Gemini to optimize the prompt for Gemma 3:1b"""
        
        optimization_prompt = f"""
        You are an expert prompt engineer specializing in XML markup tasks for smaller language models.
        
        TASK: Optimize the following prompt to work better with Gemma 3:1b (a 1B parameter model).
        
        CURRENT PROMPT CONFIGURATION:
        Title: {current_prompt['title']}
        Context: {current_prompt['context']}
        
        EXAMPLES PROVIDED:
        {json.dumps(current_prompt['examples'], indent=2, ensure_ascii=False)}
        
        PERFORMANCE ISSUES OBSERVED:
        {chr(10).join(performance_issues)}
        
        OPTIMIZATION REQUIREMENTS for Gemma 3:1b:
        1. Use MUCH simpler, clearer language - avoid complex sentences
        2. Break down complex instructions into numbered steps
        3. Reduce the context length significantly (target <1500 tokens)
        4. Use more explicit pattern examples
        5. Add clear "MUST" and "MUST NOT" rules
        6. Simplify the XML tagging explanation
        7. Keep all examples but make instructions more direct
        8. Use bullet points instead of complex paragraphs
        
        CONSTRAINTS FOR Gemma 3:1b:
        - Very limited reasoning ability compared to larger models
        - Better with explicit patterns and rules
        - Needs step-by-step instructions
        - Works better with shorter, direct prompts
        - Requires very clear examples
        
        Please provide an optimized prompt context that will work better with Gemma 3:1b while maintaining the same XML markup accuracy. Focus on clarity and simplicity.
        """
        
        try:
            response = self.gemini_model.generate_content(optimization_prompt)
            return response.text
        except Exception as e:
            print(f"Error with Gemini optimization: {e}")
            return None
    
    def test_with_gemma3_1b(self, optimized_prompt: str, test

---

