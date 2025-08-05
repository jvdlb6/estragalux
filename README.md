# Soluções Implementadas

## Índice

- [1. Correção de Bug: Ajuste de Timezone em `formatDate`](#1-correção-de-bug-ajuste-de-timezone-em-formatdate)
  - [Problema](#problema)
  - [Solução](#solução)
- [2. Funcionalidade: Filtro por Prédio em `ApartmentOwners.tsx`](#2-funcionalidade-filtro-por-prédio-em-apartmentownerstsx)
  - [Objetivo](#objetivo)
  - [Passos](#passos)

## 1. Correção de Bug: Ajuste de Timezone em `formatDate`

### Problema

- A função `formatDate` em `ApartmentOwners.tsx` fazia um ajuste manual de timezone:
  ```ts
  const adjustedDate = new Date(
    date.getTime() - date.getTimezoneOffset() * 60000,
  );
  ```
- Isso causava um ajuste duplo de fuso horário já que o JavaScript converte datas ISO para o fuso local automaticamente.

### Solução

1. **Remover o ajuste manual**:

   ```ts
   // Antes
   const adjustedDate = new Date(
     date.getTime() - date.getTimezoneOffset() * 60000
   );
   return adjustedDate.toLocaleString(...);

   // Depois
   return date.toLocaleString(...);
   ```

2. Agora, a data é exibida corretamente no horário local.

> Posteriormente a implementação do fix na tela de Payments, percebi que o bug estava presente na outras telas, portanto, fiz uma busca e removi todas as ocorrências do duplo ajuste do date

---

## 2. Funcionalidade: Filtro por Prédio em `ApartmentOwners.tsx`

### Objetivo

Permitir ao usuário filtrar os proprietários de apartamentos por prédio.

### Passos

1. **Criar estado para o filtro**

   ```ts
   const [buildingFilter, setBuildingFilter] = useState<string>('');
   ```

2. **Adicionar o componente Select para filtro**

   ```tsx
   <FormControl sx={{ minWidth: 200 }}>
     <InputLabel id="building-filter-label">
       <Box display="flex" alignItems="center">
         <FilterIcon fontSize="small" sx={{ mr: 1 }} />
         Filter by Building
       </Box>
     </InputLabel>
     <Select
       labelId="building-filter-label"
       id="building-filter"
       value={buildingFilter}
       label="Filter by Building"
       onChange={(e: SelectChangeEvent) => setBuildingFilter(e.target.value)}
     >
       <MenuItem value="">
         <em>All Buildings</em>
       </MenuItem>
       {buildingsData?.buildings.map((building: Building) => (
         <MenuItem key={building.id} value={building.id}>
           {building.name}
         </MenuItem>
       ))}
     </Select>
   </FormControl>
   ```

3. **Filtrar os proprietários conforme o prédio selecionado**

   ```ts
   const filteredOwners = data?.apartmentOwners.filter(
     (owner: ApartmentOwner) => {
       if (!buildingFilter) return true;
       return owner.building?.id === buildingFilter;
     },
   );
   ```

4. **Exibir os proprietários filtrados**
   ```tsx
   <Grid container spacing={3}>
     {filteredOwners.map((owner: ApartmentOwner) => (
       <Grid item xs={12} sm={6} md={4} key={owner.id}>
         {/* ...card do proprietário... */}
       </Grid>
     ))}
   </Grid>
   ```
