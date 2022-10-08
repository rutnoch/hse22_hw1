# hse22_hw1

Рустанович Анна БГП202

3 группа

## Обязательная часть

#### 1. Создание ссылок на файлы

```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
```

#### 2. Выбор случайных чтений

random seed = 511

кол-во чтений paired-end = 5 000 000

кол-во чтений mate-pairs = 1 500 000

```
seqtk sample -s511 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s511 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s511 oilMP_S4_L001_R1_001.fastq 1500000 > matepairs1.fastq
seqtk sample -s511 oilMP_S4_L001_R2_001.fastq 1500000 > matepairs2.fastq
```

#### 3. Проверка качетства чтений

fastQC:

```
mkdir fastqc
ls sub* matepairs* | xargs -tI{} fastqc -o fastqc {}
```

multiQC:

```
mkdir multiqc
multiqc -o multiqc fastqc
```

<img width="987" alt="Screenshot 2022-10-07 at 23 16 45" src="https://user-images.githubusercontent.com/115284128/194646230-05833ea0-c3ac-4150-ab91-2c6cce22c133.png">

<img width="968" alt="Screenshot 2022-10-07 at 23 16 12" src="https://user-images.githubusercontent.com/115284128/194646270-dfaea660-a196-4963-bf6a-5ef09724446d.png">

#### 4. Триминг чтений и удаление адаптеров

```
platanus_trim sub*
platanus_internal_trim matepair*
```

После завершения работы platanus удаление исходных файлов чтений:

```
rm sub1.fastq
rm sub2.fastq
rm matepairs1.fastq 
rm matepairs2.fastq
```

#### 5. Проверка качества обрезанных чтений с удаленными адаптерами


fastQC:

```
mkdir fastqc_trm
ls sub* matepairs* | xargs -tI{} fastqc -o fastqc_trm {}
```

multiQC:

```
mkdir multiqc_trm
multiqc -o multiqc_trm fastqc_trm
```

<img width="951" alt="Screenshot 2022-10-07 at 23 36 37" src="https://user-images.githubusercontent.com/115284128/194648815-e8861189-5482-453d-8498-7e92f9171ecc.png">

<img width="927" alt="Screenshot 2022-10-07 at 23 37 25" src="https://user-images.githubusercontent.com/115284128/194648920-9bdc7960-14c9-4f24-8a5b-c5a9a507aded.png">

#### 6. Получение контигов (platanus assemble) из подрезанных чтений

```
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```

#### 7. Сбор скаффолдов (platanus scaffold) из контигов и подрезанных чтений

```
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed matepairs2.fastq.int_trimmed 2> scaffold.log
```

#### 8. Уменьшение количества гэпов (platanus gap_close) 

```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed  matepairs2.fastq.int_trimmed 2> gapclose.log
```

#### 9. Удаление подрезанных чтений

```
rm matepairs1.fastq.int_trimmed
rm matepairs2.fastq.int_trimmed
rm sub1.fastq.trimmed
rm sub2.fastq.trimmed
```

