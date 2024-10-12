@emreokul0 ➜ /workspaces/helm (main) $ helm install elasticsearch elastic/elasticsearch -f values-elasticsearch.yaml
coalesce.go:237: warning: skipped value for elasticsearch.roles: Not a table.
Error: INSTALLATION FAILED: template: elasticsearch/templates/statefulset.yaml:313:17: executing "elasticsearch/templates/statefulset.yaml" at <has "master" .Values.roles>: error calling has: Cannot find has on type map

## Solution

Bu hata, `values-elasticsearch.yaml` dosyanızda yanlış bir yapılandırma veya eksik bir alan olduğuna işaret ediyor. Helm, `elasticsearch.roles` anahtarını bir **table** (harita veya sözlük) olarak bekliyor, ancak mevcut yapılandırmada bu doğru değil. Ayrıca, Helm, `has "master" .Values.roles` kontrolünü yaparken bir hata alıyor çünkü `roles` anahtarı düzgün bir şekilde tanımlanmamış.

### Çözüm Adımları:

1. **`values-elasticsearch.yaml` Dosyanızı Kontrol Edin:**
   Öncelikle, `values-elasticsearch.yaml` dosyanızdaki `elasticsearch.roles` anahtarını inceleyin. Bu anahtarın bir tablo (yani bir harita ya da sözlük) olarak tanımlanmış olması gerekir. Örnek bir yapı şu şekilde olmalıdır:

   ```yaml
   roles:
     master: "true"
     data: "true"
     ingest: "true"
   ```

   Eğer `roles` yanlış tanımlanmışsa veya eksikse, yukarıdaki gibi bir tablo olarak düzeltmelisiniz.

2. **Chart Değişikliklerine Uyum Sağlayın:**
   Kullanmakta olduğunuz Elasticsearch Helm Chart'ında son zamanlarda yapılandırma değişiklikleri olmuş olabilir. Bu nedenle, Chart belgelerini inceleyerek, `roles` alanının nasıl tanımlanması gerektiğini doğrulayın. Belki de Chart, artık `roles` yerine farklı bir yapı kullanıyor olabilir.

3. **Chart Sürümünü Kontrol Edin:**
   Elasticsearch Helm Chart’ının doğru sürümünü kullandığınızdan emin olun. Sürüm değişiklikleri bazen yapılandırma dosyasında farklılıklar yaratabilir. Chart sürümünü kontrol etmek için şu komutu kullanabilirsiniz:

   ```bash
   helm search repo elastic/elasticsearch --versions
   ```

   Belirli bir sürümü yüklemek için:
   ```bash
   helm install elasticsearch elastic/elasticsearch --version <version-number> -f values-elasticsearch.yaml
   ```

4. **`has` Fonksiyonunun Kullanımı:**
   Hata, `has` fonksiyonunun bir anahtar üzerinde çalışırken problem yaşadığını gösteriyor. Eğer `elasticsearch/templates/statefulset.yaml` dosyasındaki `has "master" .Values.roles` satırıyla ilgili bir sorun varsa, bu bölümde şunu kontrol etmelisiniz:
   ```yaml
   {{- if has "master" .Values.roles -}}
   ```

   Bu satır, `roles` anahtarının `master` değerini içerip içermediğini kontrol eder. `roles` yapısının yukarıda belirtilen gibi bir tablo (harita) olduğundan emin olun.

### Özetle:
- `values-elasticsearch.yaml` dosyanızdaki `roles` yapılandırmasını tablo şeklinde tanımlayın.
- Elasticsearch Helm Chart’ının belgelerini ve sürüm notlarını kontrol edin.
- Eğer gerekirse, eski sürümlerle uyumluluk sağlamak için Chart sürümünü değiştirmeyi deneyin.

Bu adımlar sorununuzu çözmelidir.