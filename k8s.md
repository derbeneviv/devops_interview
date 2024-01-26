# env vars weird helm template (*)
there are two snippets of code:  
first:  
*values.yaml*
```
env:
  VAR1: val1
  VAR2: val2
```

templates/deployment.yaml:
```
{{- range $var, $val := .Values.env }}
- name: {{ $var | quote }}
  value: {{ $val | quote }}
{{- end }}
```
second:
*values.yaml*
```
env:
  - VAR1: val1
  - VAR2: val2
```

templates/deployment.yaml:
```
{{- range $var_val := .Values.env }}
{{- range $var, $val := $var_val }}
- name: {{ $var | quote }}
  value: {{ $val | quote }}
{{- end }}
{{- end }}
```

Questions:
* There is a technical reason to do a more complex template. What is it?
* If the answer to the first question is correct - are there alternatives?

<details>
  <summary>Answer</summary>
  There are cases when order of fields is important. I.e. https://kubernetes.io/docs/tasks/inject-data-application/define-interdependent-environment-variables/  
  
  range over array of maps doesn't guarantee the order. i.e.:  
  
  ```
  env:
    VAR2: val2
    VAR1: $(VAR2)
  ```
  will render as:
  ```
  - name: "VAR1"
    value: "$(VAR2)"
  - name: "VAR2"
    value: "val1"
  ```
  which will fail to replace $(VAR2) with a value of VAR2

  Alternatives:
  * use toYaml:  
    *values.yaml*:
    ```
    env:
      - name: "VAR2"
        value: "val1"
      - name: "VAR1"
        value: "$(VAR2)"
    ```
    template:
    ```
    env:
      {{ .Values.env | toYaml | nindent 8 }}
    ```
  * try to pass variables explicitly w\o needing of interdependent values
  * put a hard-coded env var in template in the bottom of env vars
</details>

